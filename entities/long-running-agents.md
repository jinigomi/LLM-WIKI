---
title: Long-Running Agents
created: 2026-05-01
updated: 2026-05-01
type: entity
tags: [agentic-ai, agent-workflow, orchestration, context-engineering, session-lifecycle, agent-engineering, engineering]
sources: [raw/articles/long-running-agents-addy-osmani-2026.md]
confidence: high
---

# Long-Running Agents

## Overview

**한 번에 하나의 컨텍스트 윈도우에서 작업을 마치는 기존 패러다임을 넘어, 수 시간~수 일~수 주에 걸쳐 여러 컨텍스트 윈도우와 샌드박스에서 작업을 이어가는 AI 에이전트 아키텍처.** "AI가 작업을 수행한다"에서 "AI가 작업 프로그램을 유지·관리한다"로의 패러다임 전환.

Addy Osmani가 2026년 4월 제안한 이 개념은 기존 채팅 에이전트의 치명적 한계 — 컨텍스트 로트(context rot), 재회귀 버그, 빈약한 오류 복구 — 를 **구조화된 아티팩트 + 샌드박스 격리 + 교대(shift) 기반 작업**으로 해결한다.

## 핵심 구성 요소 (6가지)

```
┌─────────────────────────────────────────────────────────┐
│  Long-Running Agent Architecture                        │
├─────────────────────────────────────────────────────────┤
│  1. Task Queue & Plan (TASKS.md)                        │
│     → 작업 분할, 완료/진행 상태 추적                      │
│                                                         │
│  2. Structured Handoff Artifacts                        │
│     → PROGRESS.md, DESIGN.md, DECISIONS.md              │
│     → 교대 간 지식 전달 (shift-change notes)              │
│                                                         │
│  3. Test Plan (TEST_PLAN.md)                            │
│     → 회귀 방지, 완료 조건 검증                           │
│                                                         │
│  4. Sandbox Isolation (Docker/VM)                       │
│     → 작업 격리, 스냅샷 체크포인트, 롤백                   │
│                                                         │
│  5. Memory & State (Artifact + Vector + Git)            │
│     → 아티팩트 기억 (1차), 벡터 DB 회상 (장기), Git 추적   │
│                                                         │
│  6. Error Recovery (Checkpoint + Timeout + Escalate)    │
│     → 멱등성, 체크포인트, 타임아웃 위임, 에스컬레이션      │
└─────────────────────────────────────────────────────────┘
```

## 왜 긴 컨텍스트 윈도우로는 부족한가?

2M 토큰 컨텍스트 윈도우가 있다고 해서 해결되지 않는 근본적 문제들:

| 문제 | 설명 |
|------|------|
| **Context Rot** | 컨텍스트가 길어질수록 모델의 실질적 회상력 저하. 15턴 전의 탄젠트가 현재 추론을 오염시킴 |
| **비용 누적** | 500K 토큰 유지 시 턴당 $0.05+ 비용이 무한정 누적 |
| **보안 경계 없음** | 단일 컨텍스트 윈도우 = 단일 샌드박스. 서브태스크 오류가 전체 오염 |
| **체크포인트 부재** | "지금 멈추고 검토할 시점"이 명확하지 않음 |
| **병렬화 불가** | 멀티 에이전트·멀티 머신 작업 분산 불가 |

**해결책:** Long-running agent는 *의도적으로* 새 컨텍스트 윈도우를 사용한다. 각 윈도우는 특정 서브태스크에만 집중하는 고신호·저노이즈 환경.

## The Ralph Loop: 자기 개선 에이전트

Long-running agent의 핵심 품질 메커니즘. "Ralph"라는 코드 리뷰어의 이름에서 유래:

```
Shift N:     [에이전트 작업] → 저장 → 일시정지
                ↓
Shift N+1:   [이전 작업 리뷰] → 버그 발견 → 수정 → 새 작업 진행
               ↑ Fresh context window = 편향 없는 리뷰
```

- **핵심 인사이트:** 리뷰 단계가 새 컨텍스트 윈도우에서 실행되기 때문에, 에이전트는 원래 코드를 작성할 때의 추론 편향에 오염되지 않는다.
- 자연스러운 품질 사이클: 생산 → 리뷰 → 개선 → 생산

## 실제 적용 사례

이미 현장에서 구축 중인 사례들:

- **Multi-day 코드베이스 마이그레이션:** 프레임워크 업그레이드, 대규모 리팩토링, 언어 포팅
- **지속적 PR 리뷰:** 매일 밤 실행되어 코딩 표준 기준으로 새 PR 리뷰
- **인프라 유지보수:** 정기 보안 업데이트, 의존성 업그레이드, 설정 드리프트 감지
- **Spec-Driven Development:** 명세 문서를 읽고 구현 → 자체 테스트 → 교대 반복
- **Self-improving agents:** Ralph loop로 교대 간 자체 리뷰

## 구현 패턴

| 패턴 | 설명 |
|------|------|
| **Shift-based** | 2-4시간 단위 교대. 읽기→작업→쓰기 사이클. Cron/CI 트리거 |
| **Event-driven** | 이벤트(새 PR, 새 이슈)마다 새 컨텍스트 윈도우 |
| **Hybrid** | 정기 교대 + 이벤트 트리거. 공유 아티팩트 메모리 |

## 현재 한계

1. **표준화 부재:** "체크포인트 + 구조화 아티팩트 갖춘 long-running 에이전트" npm 패키지는 아직 없음
2. **오케스트레이션:** 의존성 있는 멀티 에이전트·멀티 샌드박스 스케줄링은 초기 단계
3. **비용 관리:** 주 단위 실행 에이전트는 신중한 컴퓨트 관리 필요
4. **관측 가능성:** 1주일째 작동 중인 에이전트 — 정체인가 진행 중인가?
5. **휴먼 핸드오프:** 언제 인간에게 에스컬레이션하고, 어떻게 명확한 상황 요약을 제시할 것인가

## 핵심 교훈

1. **아티팩트 기반 메모리 > 컨텍스트 윈도우:** TASKS.md, PROGRESS.md 같은 구조화 파일이 교대 간 신뢰성 있는 기억 매개체
2. **Git이 궁극의 진실 공급원:** 모든 의미 있는 단계를 커밋 — 불변의 감사 추적
3. **Ralph loop가 품질을 보장:** 다음 교대에서 이전 작업을 새 눈으로 리뷰 — 자연스러운 품질 게이트
4. **멱등성 설계:** 모든 작업은 안전하게 재실행 가능해야 함. 실패 시 의심하지 않고 재시도
5. **구조화 아티팩트의 이중 독자:** 기계 파싱 + 인간 가독성 둘 다 충족해야 함

## 연결 개념

- [[harness-engineering]] — Long-running agent의 기반이 되는 Harness Engineering 원리
- [[context-rot]] — 긴 컨텍스트 윈도우의 치명적 한계; long-running agent가 해결하려는 문제의 진단
- [[subagent-driven-development]] — 교대 기반 병렬 작업에 적용 가능한 Subagent 패턴
- [[오케스트레이션]] — 교대 간 작업 조율, 의존성 관리, 멀티 에이전트 스케줄링
- [[agentic-ai-engineering]] — Production 레이어의 핵심 패턴으로서의 Long-running 에이전트
- [[spec-driven-development]] — 교대 기반 구현 루프의 자연스러운 적용처
- [[기억하는-개발]] — 컨텍스트 윈도우를 넘어서는 지식 축적 패턴
- mihomo-rust-claude-code-porting ⚠️ 페이지 미생성 — Agent Team을 통한 multi-day 포팅 사례 (교대 패턴의 실전)