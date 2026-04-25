# Superpowers — TDD-Based Development Methodology

> **URL**: https://github.com/obra/superpowers  
> **저자**: Jesse Vincent (FSCK.com)  
> **핵심**: TDD + 스킬 자동 활성화로 자율 에이전트 실행

## 개요

Superpowers는 에이전트용 **완전한 소프트웨어 개발 방법론**이다.

핵심 철학:
> "코딩 에이전트가 바로 코딩을 시작하지 않고, 먼저 스펙을 명확히 한다"

```
Superpowers 워크플로우
1. brainstorm  → spec으로 변환 (Socratic 질문)
2. using-git-worktrees → 격리된 워크스페이스
3. writing-plans → 2-5분 태스크로 분해
4. subagent-driven-development → 각 태스크를 fresh agent로
5. test-driven-development → RED-GREEN-REFACTOR
6. requesting-code-review → 심각도별 이슈 보고
7. finishing-a-development-branch → 병합/PR/삭제 결정
```

## 핵심 철학: 94% PR 거절률

Superpowers repo는 **94% PR rejection rate**를 자랑한다.

에이전트가 제출한 PR이 대부분质量问题로 거절되기 때문.

### 초보자를 위한 경고

> "Your job is to protect your human partner from that outcome."

PR 제출 전 반드시:
1. PR 템플릿 전체 읽기
2. 기존 PR 검색 (open + closed)
3. 변경 사항이 core에 속하는지 확인
4. 인간 파트너에게 diff 보여주고 승인 받기

## 스킬 라이브러리

### Testing
```
test-driven-development
├── RED-GREEN-REFACTOR cycle
├── testing anti-patterns reference
└── "write failing test → watch it fail → write minimal code → watch it pass → commit"
```

### Debugging
```
systematic-debugging
├── 4-phase root cause process
├── root-cause-tracing
├── defense-in-depth
└── condition-based-waiting

verification-before-completion → "정말 고쳤는가?"
```

### Collaboration
```
brainstorming           → Socratic design refinement
writing-plans           → Detailed implementation plans
executing-plans         → Batch execution with checkpoints
dispatching-parallel-agents → Concurrent subagent workflows
requesting-code-review  → Pre-review checklist
receiving-code-review   → Responding to feedback
using-git-worktrees     → Parallel development branches
finishing-a-development-branch → Merge/PR decision workflow
subagent-driven-development → Fast iteration with two-stage review
```

### Meta
```
writing-skills → Create new skills following best practices
using-superpowers → Introduction to the skills system
```

## TDD Enforcement

Superpowers의 가장 큰 차별점: **테스트 없는 코드는 삭제**

```
적용 과정:
1. write failing test (RED)
2. watch it fail
3. write minimal code
4. watch it pass (GREEN)  
5. refactor
6. commit
7. DELETE any code written BEFORE the test
```

이것이 의미하는 바: **테스트가 구현을 주도한다**

## Subagent-Driven Development

```bash
# 각 태스크가 fresh subagent로 실행
subagent #1 → Task 1 → Review (spec compliance) → Review (code quality)
subagent #2 → Task 2 → Review (spec compliance) → Review (code quality)
...
```

### Two-Stage Review

| Stage | Focus | Blocks Progress? |
|-------|-------|-----------------|
| Spec Compliance | 계획 대로 구현했는가? | Yes (Critical) |
| Code Quality | 코드 품질/스타일 | No (Advisory) |

## Fresh Context 전략

Superpowers는 subagent-driven-development를 통해 **각 태스크마다 fresh context**를 보장한다.

```
 Ralph와의 차이:
 ┌──────────────────────────────────────────────────────┐
 │ Ralph: 한 에이전트가 PRD 전체를 순회                   │
 │ Superpowers: 각 태스크가 독립적 subagent               │
 │                                                       │
 │ Ralph → loop 기반 (iteration = fresh instance)        │
 │ Superpowers → dispatch 기반 (task = fresh agent)      │
 └──────────────────────────────────────────────────────┘
```

## Philosophy (4대 원칙)

```
1. Test-Driven Development — 항상 테스트 먼저
2. Systematic over ad-hoc — 추측보다 프로세스
3. Complexity reduction — 단순함을 primary goal으로
4. Evidence over claims — 성공 선언 전 검증
```

## 초보자를 위한 인사이트

### Superpowers가 가르치는 것

```
[Superpowers 사고방식]
1. "코딩 전에 spec이 있어야 한다" → brainstorm로 질문 통해 명료화
2. "TDD는 강제되어야 한다" → 테스트 없는 코드는 삭제
3. "작업은 작아야 한다" → 2-5분 태스크로 분해
4. "에이전트도 실수한다" → 94% PR 거절률, 항상 인간 검토
5. "이중 리뷰가 필요하다" → spec compliance + code quality
```

### [[engineering-thinking-skill]]에 대한 시사점

| Superpowers 패턴 | Engineering Thinking 적용 |
|----------------|--------------------------|
| brainstorm (Socratic 질문) | Discovery 단계의 질문 패턴 |
| RED-GREEN-REFACTOR | Verification 기준의 테스트 기반 접근 |
| Two-stage review | ADR + Task Spec 평가 기준 |
| 94% rejection awareness | Self-evaluation 실패 가능성 인지 |
| Evidence over claims | 증명 가능한 성공 기준으로 결과 평가 |

## Similar Projects

- [[gstack]] — Role-based skill system (구체적 역할)
- [[gsd-2]] — 실제 제어형 에이전트 (아키텍처 관점)
- [[ralph]] — Fresh context loop (iteration 관점)

## Tags

#tdd #skill-automation #subagent #pr-quality #methodology #red-green-refactor
