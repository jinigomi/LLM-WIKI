---
title: Subagent-Driven Development
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [agent-workflow, multi-agent, subagent, orchestration, plan-first, software-development]
sources: [raw/articles/superpowers-github.md]
confidence: high
---

# Subagent-Driven Development

Superpowers의 핵심 혁신 패턴. 하나의 큰 작업을 독립적인 서브에이전트들로 분할하여 병렬·격리 실행하고, 각 결과물을 two-stage review로 검증한다.

**출처:** [[superpowers]] → `skills/subagent-driven-development/SKILL.md`

## 작동 방식

```
Plan → [Task1 → Implementer → SpecReview → CodeQualityReview]
     → [Task2 → Implementer → SpecReview → CodeQualityReview]
     → Done
        ↑ subagent     ↑ subagent       ↑ subagent
```

각 서브에이전트는:
- **세션 히스토리를 상속받지 않음** — 정제된 task-specific 컨텍스트만 수신
- **격리된 환경** — 다른 서브에이전트의 상태에 의존하지 않음
- **Two-stage review** — 구현 완료 후 spec compliance → code quality 순차 검증

## Two-Stage Review

### Stage 1: Spec Review (Plan Alignment)
- 구현이 계획(plan)과 일치하는가?
- 모든 요구사항이 충족되었는가?
- 빠진 기능이 없는가?

### Stage 2: Code Quality Review
- TDD 사이클을 지켰는가?
- Verification evidence가 있는가?
- 코드 스타일·패턴이 일관적인가?

## 왜 효과적인가?

1. **컨텍스트 오염 방지** — 긴 세션에서 [[context-rot]]이 발생해도 서브에이전트는 fresh context로 시작
2. **병렬 처리** — 독립적인 task들은 동시에 실행 가능
3. **자율성** — 사람 개입 없이 수 시간 작업 가능 (plan만 미리 승인되면)
4. **품질 보장** — two-stage review로 각 task마다 검증

## Superpowers에서의 위치

- **writing-plans** → plan 생성
- **subagent-driven-dev** → plan 실행 (멀티 task)
- **executing-plans** → plan 실행 (싱글 task, 서브에이전트 불필요 시)
- **finishing-a-development-branch** → 모든 task 완료 후 통합

## Hermes와의 관계

[[Hermes]]의 `delegate_task`가 이 패턴의 직접적 구현체다. Superpowers의 subagent-driven-dev는 `delegate_task` + two-stage review + plan 연동을 하나의 skill로 패키징한 것.

## 참고

- [[superpowers]] — 전체 플러그인 개요
- [[오케스트레이션]] — multi-agent orchestration 일반론
- [[vibe-coding-blueprint-pattern]] — Plan-First 철학의 공통점
- [[context-rot]] — subagent isolation으로 해결하는 문제