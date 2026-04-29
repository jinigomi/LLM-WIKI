---
title: Don Cheli SDD Framework
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [spec-driven-development, agent-framework, tdd, code-quality, claude-code, codex, ollama, typescript]
sources: [https://github.com/doncheli/don-cheli-sdd]
confidence: high
---

## 개요

Don Cheli는 **Specification-Driven Development (SDD)** 를 강제하는 프레임워크다. TDD를 법으로 삼고, 6단계 파이프라인을 통해 아이디어에서 검증된 코드까지 자동으로 진행한다. "Stop guessing. Start engineering."이라는 슬로건처럼, AI 코딩의 불확실성을 제거하는 것을 목표로 한다.

- **저자:** Jose Luis Oronoz Troconis (@DonCheli)
- **라이선스:** Apache 2.0
- **언어:** TypeScript (런타임), Markdown (스킬/커맨드)
- **지원 에이전트:** Claude Code, Codex, Cursor, Gemini/Antigravity, OpenCode, Qwen, Windsurf
- **Graphify:** 138 노드 · 212 엣지 · 12 커뮤니티

## 핵심 설계 철학

Don Cheli는 "파일 기반 계약(contract)"으로 동작한다. 런타임이 AI 에이전트를 Docker 컨테이너 안에서 직접 실행하고, 각 단계마다 품질 게이트를 통과해야만 실제 프로젝트에 코드가 머지된다.

```text
아이디어 → /dc:specify → /dc:clarify → /dc:tech-plan
→ /dc:breakdown → /dc:implement → /dc:review → ✅ 검증 완료
```

## 아키텍처 레이어

### Layer 1: 컨텍스트 계약 (Markdown)
AGENTS.md → CLAUDE.md, GEMINI.md, .cursorrules, prompt.md
- 각 에이전트마다 별도 어댑터 파일이 있지만, 핵심 로직은 공유
- 93개 `/dc:*` 커맨드 + 51개 스킬을 파일로 배포

### Layer 2: 프로젝트 상태 (.dc/)
```text
.dc/
├── config.yaml    # 프로젝트 설정
├── estado.md      # 현재 상태
├── plan.md        # 기술 계획
├── progreso.md    # 진행 상황
└── gates/         # 커스텀 품질 게이트 (YAML)
```

### Layer 3: 런타임 (TypeScript)
실제 실행 엔진. Graphify 상 **God Node**: `orchestrate()` (31 엣지).

```text
runtime/src/
├── orchestrator.ts   # 파이프라인 오케스트레이션 (16KB, 413줄)
├── sandbox/
│   ├── worktree.ts   # Git worktree 격리
│   ├── docker.ts     # Docker 컨테이너 실행
│   └── local.ts      # 로컬 실행 (fallback)
├── providers/
│   ├── claude.ts     # Claude Code CLI
│   ├── codex.ts      # OpenAI Codex
│   └── ollama.ts     # Ollama 로컬 모델
├── gates/
│   ├── spec-gate.ts  # Gherkin 스펙 검증
│   ├── tdd-gate.ts   # TDD 검증 (테스트 존재, 통과, 커버리지 ≥85%)
│   └── custom-gate.ts # 사용자 정의 게이트
└── utils/
    ├── state.ts      # StateManager: 파이프라인 상태 추적
    ├── cost.ts       # 비용 추정
    └── logger.ts     # 로깅
```

### 핵심 워크플로우

1. **Git worktree 생성** — 실제 프로젝트의 분리된 복사본
2. **Docker 컨테이너 실행** — 완전 격리된 환경
3. **AI 에이전트가 `/dc:*` 커맨드 실행** — Docker 안에서
4. **품질 게이트 검증** — 각 단계마다 통과해야 함
5. **전체 통과 시에만 머지** — 실패하면 worktree 폐기

## 6단계 파이프라인

| 단계 | 커맨드 | 결과물 |
|------|--------|--------|
| Specify | `/dc:specify` | Gherkin 시나리오 + DBML 스키마 |
| Clarify | `/dc:clarify` | 모호성 해소 (가상 QA) |
| Plan | `/dc:tech-plan` | 아키텍처 + API 계약 |
| Breakdown | `/dc:breakdown` | TDD 태스크 + 병렬화 |
| Implement | `/dc:implement` | 테스트 → 코드 → 리팩터 |
| Review | `/dc:review` | 7차원 피어 리뷰 |

## 강점

- **실제 런타임이 강제** — 다른 SDD 도구와 달리, 프롬프트만 던지는 게 아니라 Docker 안에서 직접 실행하고 게이트를 검증
- **프로젝트 무결성** — worktree + Docker 격리로 실패해도 실제 코드에 영향 없음
- **다양한 AI 제공자** — Claude Code(무료), Codex, Ollama(로컬/무료)
- **커스텀 게이트** — `.dc/gates/*.yml`로 품질 기준을 자유롭게 확장
- **15개 추론 모델** — Pre-mortem, 5 Whys, Pareto, RLM 등
- **언어 지원** — ES/EN/PT (스페인어가 기본)

## 약점

- **Docker 의존성** — 로컬 fallback이 있지만 기본 경로는 Docker 필수
- **복잡한 설치** — `npm install -g don-cheli-sdd` + `don-cheli install --global`의 2단계
- **스페인어 중심** — 커맨드가 `/dc:especificar` 등 스페인어. 영어 사용자에게 진입 장벽
- **Graphify 커뮤니티 cohesion 낮음** — Community 0 (0.08), Community 1 (0.12)의 낮은 응집도는 모듈화 개선 여지 시사

## 연결 개념

- [[spec-driven-development]] — SDD 방법론의 기본 개념. Don Cheli는 이 개념을 Docker 런타임으로 구현
- [[cc-sdd]] — 같은 SDD 도메인의 경쟁 프레임워크. Kiro 스타일, Agent Skills 중심
- [[harness-engineering]] — AI 에이전트 행동 제어의 상위 개념. Don Cheli는 harness의 한 구현체