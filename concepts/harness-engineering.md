---
title: Harness Engineering
created: 2026-04-27
updated: 2026-04-27
type: concept
tags:
- agentic-ai
- aws
- infrastructure
- deep-insight
- multi-agent
sources:
- https://aws.amazon.com/ko/blogs/tech/harness-engineering-from-deep-insight/
confidence: high
---

# Harness Engineering

## Overview

AI 에이전트를 **로컬 개발에서 프로덕션 운영까지** 안정적으로 동작하게 하는 엔지니어링 분야. "마구(馬具)"처럼 에이전트의 행동을 묶고, 방향을 잡고, 안전하게 제어하는 구조를 설계하는 것.

## 등장 배경

2024년 하반기 ~ 2026년 상반기, AI 에이전트가 PoC를 넘어 프로덕션 배포의 현실적 과제에 직면한 시기.

- Gartner 전망: 2028년까지 기업 소프트웨어의 33%가 Agentic AI 포함
- Anthropic의 "Building Effective Agents"가 7가지 설계 패턴 제시
- MCP, A2A 등 상호운용성 표준 등장
- **그러나**: 에이전트의 추론/오케스트레이션에 집중, **인프라 설계와 운영 엔지니어링은 공백**으로 남아있었음

### 학계/산업계 동향

- LLM 시스템 성능이 **모델보다 주변 하네스 코드에 더 크게 좌우**된다는 실증 결과
- 프롬프트 인젝션 공격 평균 성공률 **84.30%**
- OWASP "Agentic AI Threats and Mitigations" (2025년 2월)
- AWS Amazon Bedrock AgentCore 등 **관리형 에이전트 인프라** 경쟁적 출시

## 하네스 엔지니어링의 정의

AI 에이전트가 실행되는 **제어 환경과 규칙 모음**. 구체적 포함 범위:

| 요소 | 설명 |
|------|------|
| **도구 (Tool)** | 에이전트에게 주어지는 도구 목록과 권한 범위 |
| **Context 관리** | 에이전트에 들어가는 Context 범위 관리 |
| **실행 환경** | 에이전트가 호출하는 API/함수의 실행 환경 |
| **로깅/모니터링** | 감사 추적 및 성능 관측 레이어 |

동일한 LLM이라도 **하네스를 어떻게 설계하느냐**에 따라 에이전트의 성능과 안정성이 크게 달라짐.

## Deep Insight 사례

사용자가 업로드한 CSV + 분석 질문 → 최종 DOCX 리포트 생성하는 프로덕션 Multi-Agent 시스템.

### 두 가지 설계 목적

1. **에이전트 행동 제어** — "에이전트는 항상 올바른 결과를 도출, 검증 가능해야 함" (Part 2)
2. **인프라 설계** — "에이전트 시스템은 안전한 환경에서 안정적으로 실행" (Part 3)

### Decision 1: 코드 실행 환경의 완전한 분리

**문제**: 로컬에서 `subprocess.run()`로 코드 실행 → 프로덕션에서 다중 요청 시 줄줄이 실패

**분석**:
- 보안 위험: LLM이 생성한 악성코드/무한루프가 Runtime에 영향
- 워크로드 불일치: Agent Runtime은 I/O-wait 지배적, CPU-heavy 분석에 부적합
- 확장성 한계: Runtime과 코드 실행이 묶이면 개별 스케일링 불가

**해결**: 코드 생성(LM 추론) → AgentCore Runtime, 코드 실행(compute) → AWS Fargate

### AWS Fargate 선택 이유

| 옵션 | 장점 | 제약/단점 |
|------|------|-----------|
| EC2 | 유연한 환경 구성 | 스케일링/패치 관리 부담 |
| Lambda | 서버리스 간편함 | 실행 시간 15분 제한 |
| **AWS Fargate** | **서버 관리 부담 없음, 시간 제한 없음** | — |

Fargate Docker 이미지로 **한글 폰트, matplotlib, seaborn** 등 분석 환경 자유롭게 구성.

### 2단계 코드 실행 구조

1. **파일 쓰기** (in-process): Base64 인코딩으로 특수문자/한글/ escape 우회
2. **코드 실행** (subprocess + timeout): `BASH:` prefix + timeout으로 무한루프 격리

핵심: "파일 쓰기"와 "코드 실행" 문제를 격리 → 안전하게 LLM 코드의 불확정성 흡수

### ALB + Fargate 아키텍처

```
AgentCore Runtime → ALB → Fargate Task Pool
```

- **요청 분배**: 다중 사용자 동시 분석 요청 처리
- **Health Check 게이트**: Task 준비 완료 후에만 트래픽 전송
- **Sticky session**: 동일 분석 세션의 후속 요청이 같은 Fargate Task로 라우팅

### GlobalFargateSessionManager

Fargate Task의 **전체 생명주기 관리**:
1. 분석 요청 시 새 Fargate Task 프로비저닝
2. ALB Target Group에 등록 + Health Check 대기
3. 세션별 고유 HTTP Cookie 발급 (Sticky session)
4. 분석 완료 후 Target Group에서 제거 + 컨테이너 종료

→ Runtime은 에이전트 추론에만 집중, 무거운 compute는 Fargate에 위임

### Decision 2: S3를 중간 저장소로 활용

S3 선택 이유: 99.999999999% 내구성, 무제한 확장성, VPC Endpoint, 저렴한 비용

**S3 prefix 구조** (세션별 격리):
```
s3://bucket/deep-insight/fargate_sessions/{session_id}/
├── input/        # 원본 데이터
├── artifacts/    # 생성된 분석 산출물 (all_results.txt, citations.json, 차트 PNG)
├── debug/        # 실행별 로그
└── output/       # 세션 메타데이터 (token_usage.json)
```

**S3의 세 가지 용도**:

1. **Fargate 결과물 보존**: matplotlib 그래프, 전처리 CSV 등 → 세션 완료 시 S3 일괄 업로드
2. **Context 외부화 (외부 메모리)**: 에이전트 생성 정보를 artifacts/ 파일로 저장 → Context는 "N단계 완료, 상세 내용은 파일 참조" 수준으로 가볍게 유지
3. **Human-in-the-loop 피드백**: S3를 통해 사용자가 에이전트 계획 검토/개입

## 핵심 교훈

1. **에이전트 추론과 코드 실행을 분리하라** — 각각 독립적 인프라에서 개별 스케일링
2. **S3를 외부 메모리로 활용하라** — Context 한계를 넘기 위해 에이전트 산출물을 S3에 외부화
3. **하네스는 검증 가능성을 제공해야 한다** — Validator/Tracker로 에이전트 결과 검증
4. **네트워크 격리는 기본이다** — VPC 모드 + AWS PrivateLink로 분석 데이터가 퍼블릭 인터넷을 경유하지 않도록

## 관련 개념

- [[context-window]] — Context Engineering (Part 2)
- [[multi-agent]] — Deep Insight의 Multi-Agent 설계
- [[agentic-ai]] — Agentic AI 시스템 설계 패턴