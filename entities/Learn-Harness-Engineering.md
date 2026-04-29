---
title: Learn Harness Engineering
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [entity, agent-engineering, harness, coding-agent, course, electron, claude-code, codex, progressive-disclosure, verification, scope-control, session-lifecycle]
confidence: high
source: https://github.com/walkinglabs/learn-harness-engineering
source-type: github-repo
analyzed-by: graphify-v2
---

# Learn Harness Engineering

WalkingLabs의 AI 코딩 에이전트 Harness Engineering 프로젝트 기반 과정.

> "The model is smart. The harness makes it reliable."

## 핵심 철학

Harness Engineering = 모델을 더 똑똑하게 만드는 게 아니라, **모델이 작동하는 환경을 설계**하는 것. 좋은 프롬프트보다 좋은 환경이 더 중요하다.

**증거**: Anthropic의 controlled experiment — 동일 모델(Opus 4.5), 동일 태스크. harness 없이: $9/20분/작동 안 함. harness 있이: $200/6시간/실제 플레이 가능한 게임.

## Harness의 5대 서브시스템

| 서브시스템 | 역할 | 구현체 |
|-----------|------|--------|
| **Instructions** | 에이전트에게 무엇을, 어떤 순서로 할지 알려줌 | `AGENTS.md`, `CLAUDE.md`, `docs/` |
| **State** | 무엇이 완료됐고 진행 중인지 추적 | `progress.md`, `feature_list.json`, git log |
| **Verification** | 통과하는 테스트만이 증거 | tests, lint, type-check, smoke runs, e2e |
| **Scope** | 한 번에 한 기능만 | feature_list.json (one at a time) |
| **Session Lifecycle** | 세션 시작 시 init, 끝날 때 clean state | `init.sh`, handoff notes, clean commit |

## 커리큘럼 구조

**6 Phase, 12 Lectures + 6 Projects**

```
Phase 1: SEE THE PROBLEM
  L01: Strong models ≠ reliable execution
  L02: What harness actually means
  → P01: Prompt-only vs. rules-first comparison

Phase 2: STRUCTURE THE REPO
  L03: Repository as single source of truth
  L04: Split instructions across files (progressive disclosure)
  → P02: Agent-readable workspace

Phase 3: CONNECT SESSIONS
  L05: Keep context alive across sessions
  L06: Initialize before every agent session
  → P03: Multi-session continuity

Phase 4: FEEDBACK & SCOPE
  L07: Draw clear task boundaries
  L08: Feature lists as harness primitives
  → P04: Runtime feedback to correct agent behavior

Phase 5: VERIFICATION
  L09: Stop agents from declaring victory early
  L10: Full-pipeline run = real verification
  → P05: Agent verifies its own work

Phase 6: PUT IT ALL TOGETHER
  L11: Make agent's runtime observable
  L12: Clean handoff at end of every session
  → P06: Build a complete harness (capstone)
```

### Lecture 주제

| # | 질문 | 핵심 아이디어 |
|---|------|-------------|
| 01 | 왜 강한 모델도 실제 태스크에서 실패하는가? | 벤치마크 vs 실제 엔지니어링 간 capability gap |
| 02 | "harness"가 실제로 무엇인가? | 5대 서브시스템 |
| 03 | 왜 레포가 진실의 단일 원천이어야 하는가? | 에이전트가 볼 수 없으면 존재하지 않는다 |
| 04 | 왜 하나의 거대한 instruction 파일이 실패하는가? | Progressive disclosure: 백과사전 대신 지도 |
| 05 | 왜 장기 실행 태스크가 연속성을 잃는가? | Progress를 디스크에 지속 |
| 06 | 왜 초기화가 독립된 phase가 필요한가? | 작업 시작 전 환경 건강 확인 |
| 07 | 왜 에이전트가 과도하게 범위를 넓히고 덜 완료하는가? | 한 번에 한 기능, 명시적 done 정의 |
| 08 | 왜 feature list가 harness 프리미티브인가? | 에이전트가 무시할 수 없는 machine-readable scope |
| 09 | 왜 에이전트가 너무 일찍 승리를 선언하는가? | Verification gap: confidence ≠ correctness |
| 10 | 왜 e2e 테스트가 결과를 바꾸는가? | 전체 파이프라인 실행만이 진짜 검증 |
| 11 | 왜 observability가 harness 안에 있어야 하는가? | 에이전트가 한 일을 볼 수 없으면 고칠 수도 없다 |
| 12 | 왜 모든 세션이 clean state를 남겨야 하는가? | 다음 세션의 성공은 이번 세션의 정리에 달렸다 |

## 에이전트 세션 라이프사이클

```
START
  1. AGENTS.md / CLAUDE.md 읽기
  2. init.sh 실행 (install + verify + health check)
  3. claude-progress.md 읽기 (지난 세션 내용)
  4. feature_list.json 읽기 (완료/미완료)
  5. git log 확인 (최근 변경)

SELECT
  6. 정확히 하나의 미완료 feature 선택
  7. 그 feature만 작업

EXECUTE
  8. 구현
  9. verification 실행 (tests, lint, type-check)
  10. 실패 → 수정 후 재실행
  11. 통과 → 증거 기록

WRAP UP
  12. claude-progress.md 업데이트
  13. feature_list.json 업데이트
  14. 깨진 것/미검증 기록
  15. 안전하게 resume 가능할 때만 commit
  16. 다음 세션을 위한 clean restart path
```

## Capstone 프로젝트

**Electron 기반 개인 지식 베이스 데스크톱 앱**

```
┌──────────────────────────────────────────────┐
│          Knowledge Base Desktop App           │
│  ┌──────────┐  ┌───────────────────────────┐  │
│  │Doc List  │  │Q&A Panel                  │  │
│  │          │  │Q: What is harness eng?    │  │
│  │doc-001   │  │A: The environment built   │  │
│  │doc-002   │  │   around an agent model... │  │
│  │...       │  │   [citation: doc-002.md]   │  │
│  └──────────┘  └───────────────────────────┘  │
│  Status: 42 docs | 38 indexed | last sync 3m │
└──────────────────────────────────────────────┘
```

6개 프로젝트 전부 이 앱 위에서 진행. P(N+1) starter = P(N) solution. 앱이 진화하면서 harness skill도 성장.

## 기술 스택

- **Electron** (Main + Renderer + Preload)
- **TypeScript + React** (Renderer)
- **서비스**: DocumentService, IndexingService, QaService, PersistenceService
- **IPC**: contextBridge로 typed API 노출 (IPC_CHANNELS 상수)
- **데이터**: 로컬 JSON/text 파일 (DB 없음)
- **Mock Q&A**: structured answers with citations (실제 LLM API 없음)
- **문서**: VitePress, 4개 국어 (EN/ZH/RU/VI)

## Harness 파일 구성 (Quick Start)

```
project-root/
├── AGENTS.md           # 에이전트 운영 매뉴얼
├── CLAUDE.md           # Claude Code 대체
├── init.sh             # install + verify + start
├── feature_list.json   # 기능 목록, 완료 상태
├── claude-progress.md  # 세션별 진행 기록
└── src/                # 실제 코드
```

4개 파일만 추가해도 세션 안정성이 크게 향상.

## Graphify 분석

- **490 nodes · 624 edges · 174 communities** (343 files, 306K words, AST-only)
  - 프로젝트 복제(starter/solution)로 인해 실제보다 노드/커뮤니티 수 부풀려짐
- **추출**: 84% 추출, 16% 추론 (edges), 평균 confidence 0.8

### God Nodes (중심 추상화 — 커널 서비스 기준)
| Rank | Node | Edges |
|------|------|-------|
| 1 | `PersistenceService` | 29 |
| 2 | `DocumentService` | 24 |
| 3 | `QaService` | 23 |
| 4 | `IndexingService` | 23 |
| 5 | `initializeServices()` | 19 |
| 6 | `registerIpcHandlers()` | 18 |
| 7 | `createWindow()` | 16 |
| 8 | `Logger` | 16 |

### 강결합 커뮤니티 (Cohesion > 0.6 — 시뮬레이션/벤치마크)
| Community | Cohesion | 성격 |
|-----------|----------|------|
| 22 | 1.0 | (빈 커뮤니티, 프로젝트 복제 artifacts) |
| 9 | 0.67 | Handoff 시뮬레이션 |
| 10 | 0.62 | Structured logging pipeline |
| 11 | 0.72 | Init 스크립트 검증 |
| 12 | 0.61 | Harness vs non-harness 비교 |

### 아키텍처 특성
- **Electron 3-process**: Main(IPC, 서비스) → Preload(contextBridge) → Renderer(React UI)
- **Shared types**: `src/shared/types.ts` — IPC 채널, cross-boundary 인터페이스
- **Progressive disclosure**: 짧은 AGENTS.md → focused docs 링크
- **Bilingual**: EN+ZH (같은 파일에 병기), resources/는 언어별 분리

## 대상 독자

- 이미 코딩 에이전트를 사용 중인 엔지니어
- Harness 디자인의 체계적 이해가 필요한 연구자/빌더
- 환경 설계가 에이전트 성능에 미치는 영향을 이해해야 하는 테크 리드

## 레퍼런스

- [OpenAI: Harness engineering](https://openai.com/index/harness-engineering/)
- [Anthropic: Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Anthropic: Harness design for long-running app dev](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [[VibeCoding-Beginner-Research|바이브코딩 연구]] — 연관된 초보자 AI 협업 방법론 연구
- [[Karpathy-Guidelines|Karpathy Guidelines]] — 코딩 시 따라야 할 행동 지침