---
title: Superpowers
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [agent-workflow, agentic-ai, claude-code, AI-collaboration, multi-agent, methodology, plan-first, software-development, skills]
sources: [raw/articles/superpowers-github.md]
confidence: high
---

# Superpowers

**obra/superpowers** — AI 코딩 에이전트에게 "초능력"을 부여하는 플러그인. 단순한 도구 모음이 아니라, **완전한 소프트웨어 개발 방법론**을 정의한다.

- **GitHub:** https://github.com/obra/superpowers
- **버전:** v5.0.7
- **제작자:** Jesse Vincent (obra) — 전 Tailscale·Keyboardio 창업자, 現 Anthropic
- **지원 플랫폼:** Claude Code, Codex CLI, OpenCode, Gemini CLI, Cursor, Copilot CLI, Aider (7종)

## 핵심 설계 철학

| 원칙 | 설명 |
|------|------|
| **"Your human partner"** | 사용자를 "user"가 아닌 협력자로 호칭 |
| **Iron Law** | 절대적 규칙, 타협 불가 (TDD, 디버깅, 검증) |
| **Hard Gate** | 조건 충족 전까지 진행 금지 |
| **Evidence over claims** | 증거 없는 완료 주장 = 거짓말 |
| **No performative agreement** | "You're absolutely right!" 같은 의례적 동의 금지 |

## 아키텍처

```
superpowers/
├── skills/          ← 13개 skill — 핵심 두뇌
├── agents/          ← code-reviewer 서브에이전트 정의
├── hooks/           ← SessionStart: using-superpowers 자동 로드
├── docs/            ← 설계 문서 (plans/, specs/)
└── commands/        ← deprecated (skills로 완전 대체)
```

## 핵심 Skills

### 워크플로우 파이프라인
- **using-superpowers** — entrypoint, 모든 세션 시작 시 자동 로드
- **brainstorming** → **writing-plans** → **subagent-driven-dev** — 아이디어→설계→구현 파이프라인
- **executing-plans** — 싱글 세션 계획 실행 (멀티 작업은 subagent-driven-dev로)

### 품질 관리
- **test-driven-development** — RED-GREEN-REFACTOR, "한 번만 건너뛰자"는 합리화 방어
- **systematic-debugging** — 4단계 근본원인 분석
- **verification-before-completion** — 완료 주장 전 fresh 증거 필수
- **requesting-code-review** → **receiving-code-review** — two-stage 리뷰 생태계

### 협업 도구
- **subagent-driven-development** — 멀티 에이전트 병렬 구현 (핵심 혁신)
- **dispatching-parallel-agents** — 독립적 문제 도메인별 병렬 디스패치
- **using-git-worktrees** — 격리된 작업공간
- **finishing-a-development-branch** — 브랜치 완료 워크플로우

### 메타
- **writing-skills** — "TDD를 문서화에 적용" — skill 작성도 RED-GREEN-REFACTOR

## Subagent-Driven Development (핵심 혁신)

```
Plan → [Task1 → Implementer → SpecReview → CodeQualityReview] → [Task2 → ...] → Done
         ↑ subagent          ↑ subagent       ↑ subagent
```

- **Fresh context per task** — 서브에이전트마다 정제된 컨텍스트만 수신
- **Two-stage review** — spec compliance → code quality 순차 검증
- **Autonomous hours** — 사람 개입 없이 수 시간 자율 작업 가능

## 일반 AI 코딩 vs Superpowers 적용

| 차원 | 일반 AI 코딩 | Superpowers 적용 |
|------|------------|-----------------|
| 작업 시작 | 바로 코드 작성 | Brainstorm → Design 승인 → Plan |
| 버그 대응 | 증상 기반 빠른 패치 | 4단계 근본원인 분석 |
| 코드 품질 | 구현 후 검토 (생략 빈번) | TDD + Two-stage review |
| 완료 판단 | "Probably works" | Fresh verification evidence |
| PR 품질 | 가변적 | 94% PR rejection rate → 보호 |

## [[Hermes]]에 적용 가능한 패턴

1. **Two-stage review** — 코드 리뷰를 spec compliance + code quality로 분리
2. **Hard gate** — 구현 전 설계 승인 같은 게이트를 skill에 내장
3. **Subagent isolation** — 세션 컨텍스트 상속 없이 정제된 프롬프트만 전달
4. **Rationalization defense** — "이번 한 번만" 방어 멘트를 skill에 포함
5. **Evidence-before-claims** — 검증 게이트 패턴

## 참고

- [[subagent-driven-development]] — Superpowers의 핵심 패턴 상세
- [[기억하는-개발]] — Superpowers의 "accumulating context" 철학과 연결
- [[오케스트레이션]] — multi-agent orchestration 관점
- [[vibe-coding-blueprint-pattern]] — Plan-First 철학의 공통점
- [[에러-에스컬레이션]] — Systematic Debugging의 에스컬레이션 패턴과 비교