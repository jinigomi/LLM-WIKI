---
title: Agentic AI Engineering
created: 2026-05-01
updated: 2026-05-01
type: concept
tags: [agentic-ai, ai-ml, agent-framework, multi-agent, coding-agent, engineering, education]
sources: [raw/articles/agentic-ai-engineer-roadmap-2026-qa.md]
confidence: high
---

# Agentic AI Engineering

## 정의
LLM 기반 자율 에이전트 시스템을 설계·구현·배포·운영하는 소프트웨어 엔지니어링 분야. 단순 프롬프트를 넘어 **상태 관리, 도구 통합, 메모리 설계, 멀티 에이전트 오케스트레이션, 평가·모니터링, 프로덕션 배포**를 포함.

## 핵심 3계층

```
┌─────────────────────────────────────────┐
│  Layer 3: Production (Deployment)       │
│  FastAPI, Streamlit, Docker, AWS        │
├─────────────────────────────────────────┤
│  Layer 2: Agent Architecture            │
│  State, Graph, Memory, Multi-Agent      │
│  LangGraph / CrewAI / AutoGen           │
├─────────────────────────────────────────┤
│  Layer 1: Foundation                    │
│  Python, LLM, Prompts, Schemas          │
└─────────────────────────────────────────┘
```

### Layer 1 — Foundation
- **Python**: 타입 힌트(mypy), Pydantic 모델, async/await, 컨텍스트 매니저
- **LLM 이해**: 토큰, 컨텍스트 윈도우, temperature, top-p, stop sequences
- **Schema-first**: 모든 입력·출력·상태를 Pydantic으로 엄격히 타이핑

### Layer 2 — Agent Architecture
- **State**: typed, minimal, replayable — 워크플로우의 단일 진실 공급원
- **Graph-based 실행**: Node(단계) + Edge(전이)로 행동 모델링. 체크포인트, 정책 경계, 재시도 내장
- **Memory**: Short-term(컨텍스트 윈도우), Long-term(DB+Vector), Checkpoint(복원)
- **Router-Verifier 패턴**: Router → Tool Executor → Verifier → 응답

### Layer 3 — Production
- **API**: FastAPI + Pydantic으로 엔드포인트 노출
- **UI**: Streamlit/Gradio 데모
- **Deploy**: Docker + AWS/GCP, 로깅 + 모니터링 + 비용 추적

## 핵심 원칙

### 1. 프레임워크 ≠ 아키텍처
> "Frameworks are implementation tools; architecture is your state model, tool boundaries, data contracts, and safety rules."

- LangGraph, CrewAI, AutoGen은 도구다. 아키텍처는 따로 설계한다.
- 공급자 중립적: LLM·embedding 호출을 인터페이스로 추상화 → 벤더 종속 회피
- [[spec-driven-development]]와 같은 원칙 — 코드 전에 계약(contract)을 먼저 설계

### 2. Foundation-First 접근
프레임워크를 만지기 전에:
1. Python fundamentals 탄탄히
2. LLM 내부 동작 이해 (토큰화, attention, temperature)
3. 프롬프트 엔지니어링 (few-shot, CoT, system/user 분리)
4. Schema/Pydantic 설계 능력
→ 이후에야 프레임워크 도입

### 3. Fail-Closed 설계
- 스키마 검증 실패 → graceful fallback (단순 도구, 명확 질문)
- 실패 분류: transient(재시도 + backoff) vs persistent(폴백)
- "Fail open" 금지 — 검증 실패 시에도 응답을 생성하지 않을 것

### 4. 평가를 설계의 일부로
- Golden set 구축: 대표 입력 + 기대 출력 쌍
- 지표: precision, recall, faithfulness, tool-call accuracy
- A/B 비교 + 휴먼 리뷰
- 모범 답변은 항상 평가 계획을 포함해야 함

## Multi-Agent 판단 기준

Single agent with tools로 충분한 경우가 대부분. Multi-agent 도입은:
- 작업이 진정한 전문화를 요구할 때만 (planner-retriever-executor-critic)
- coordination cost > specialization benefit이면 single agent 유지
- Supervisor 패턴: 라우팅 + 결과 병합. 복잡도는 증가함

> See: [[오케스트레이션]], [[subagent-driven-development]]

## Framework 선택 가이드

| Framework | 강점 | 적합 상황 |
|-----------|------|----------|
| LangGraph | 명시적 그래프, 상태 체크포인트, HITL | 결정론적 워크플로우, 프로덕션 |
| CrewAI | Role-based 협업, 빠른 프로토타입 | 역할 분담이 명확한 팀 구조 |
| AutoGen | 에이전트 간 대화 패턴, 유연성 | 자유로운 multi-agent 대화 실험 |

## 연결 개념
- [[agentic-ai-engineer-roadmap-2026-qa]] — 이 페이지의 원천 소스 (전체 Q&A)
- [[spec-driven-development]] — schema-first, contract-first 접근
- [[오케스트레이션]] — multi-agent supervisor 패턴
- [[subagent-driven-development]] — 실전 multi-agent 구현 (Two-Stage Review)
- [[llm-tdd-quality]] — AI 평가·품질 방법론
- [[mcp-프로토콜]] — 표준화된 tool integration
- [[harness-engineering]] — 에이전트 행동 통제·예측 엔지니어링
- [[openspec]] — AI-native spec-driven 구현체
- [[에러-에스컬레이션]] — 실패 분류와 graceful degradation

## 학습 체크리스트 (요약)
1. Python + Pydantic + async 마스터
2. LLM fundamentals (토큰화, attention, 컨텍스트 윈도우)
3. 프롬프트 엔지니어링 (system/user/tool 메시지 설계)
4. 작은 툴 호출 파이프라인 구현 (Router-Verifier)
5. RAG 시스템 (청크 전략, 하이브리드 검색, 재랭킹)
6. Mem0 / 체크포인팅으로 장기 메모리 추가
7. Multi-agent 실험 (필요할 때만)
8. FastAPI + Docker + AWS 배포
9. Golden set 구축 + 평가
10. 실패/수정 로그 습관화 (면접관이 가장 듣고 싶어하는 것)