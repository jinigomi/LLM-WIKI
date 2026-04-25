# Ralph — Fresh Context Loop

> **URL**: https://github.com/snarktank/ralph  
> **저자**: Ryan Carson (based on Geoffrey Huntley's Ralph pattern)  
> **핵심**: 각 iteration = fresh AI instance + clean context

## 개요

Ralph는 **PRD(제품 요구사항 문서)** 기반 autonomous agent loop다.

```
Ralph의 동작 방식
┌─────────────────────────────────────────────────────────┐
│  1. Read prd.json (어떤 스토리가 완료되었는가?)          │
│  2. Read progress.txt (이전 iteration에서 배운 것)        │
│  3. Pick highest priority story where passes: false      │
│  4. Implement that ONE story                             │
│  5. Run quality checks (typecheck, test, lint)           │
│  6. Update prd.json → passes: true                       │
│  7. Append learnings to progress.txt                     │
│  8. Update AGENTS.md (코드 패턴 저장)                     │
│  9. Commit with message: `feat: [ID] - [Title]`         │
│ 10. Repeat until all stories pass                        │
└─────────────────────────────────────────────────────────┘
```

## 핵심 패턴: Fresh Context Per Iteration

### 왜 중요한가?

```
LLM의 Context Window 문제:
┌────────────────────────────────────────────┐
│                                            │
│  토큰 증가 → 성능 비선형 저하               │
│  (Anthropic blog: Harness Design)          │
│                                            │
│  50k+ 토큰부터 불안정                       │
│  100k+ 토큰에서 급격한 품질 저하            │
│                                            │
└────────────────────────────────────────────┘
```

Ralph는 이 문제를 **아예 생성하지 않도록** 한다:
- 각 iteration이 fresh AI instance
- 이전 iteration의 memory는 **파일로 지속** (prd.json, progress.txt)
- 새로운 iteration은 필요한 것만 파일에서 로드

## Memory Persistence (3가지途径)

### 1. Git History

```bash
# 각 iteration이 commit
git commit -m "feat: #42 - Add login form validation"
git log --oneline -10
```

### 2. progress.txt (Append-Only Learnings)

```bash
# 형식
## Codebase Patterns
- Example: Use `sql<number>` template for aggregations
- Example: Always use `IF NOT EXISTS` for migrations

## 2025-04-25 - #42
Thread: https://ampcode.com/threads/xxx
- What was implemented: Login form validation
- Files changed: src/forms/LoginForm.tsx, tests/LoginForm.test.tsx
- Learnings for future iterations:
 - Patterns discovered: form uses `useForm` hook from utils
 - Gotchas: validation rules must be in separate config file
 - Useful context: login page is at /auth/login
---
```

### 3. prd.json (Story Completion State)

```json
{
  "userStories": [
    {
      "id": "auth-001",
      "title": "User login with email and password",
      "priority": 1,
      "passes": true,
      "branchName": "feature/login"
    },
    {
      "id": "auth-002", 
      "title": "Password reset flow",
      "priority": 2,
      "passes": false,
      "branchName": "feature/password-reset"
    }
  ]
}
```

## Ralph vs Other Loop Patterns

```
┌─────────────────────────────────────────────────────────────┐
│                     Loop Patterns 비교                        │
├─────────────┬───────────────┬───────────────┬───────────────┤
│             │    Ralph      │   Superpowers │    GSD-2      │
├─────────────┼───────────────┼───────────────┼───────────────┤
│ Fresh ctxt  │ iteration마다 │ subagent마다  │ phase마다     │
│ Memory      │ 파일 (git/db) │ skill files   │ DB (memories) │
│ Granularity │ story 단위    │ task 단위     │ milestone 단위 │
│ Stop cond   │ passes all    │ tasks done    │ milestone cmpl│
│ Orchestrato │ bash loop     │ skill dispatch│ Pi SDK        │
│ Loop ctrl   │ ralph.sh      │ /subagent-drvn│ 5-wave SM     │
└─────────────┴───────────────┴───────────────┴───────────────┘
```

### Ralph의 차별점

| 특성 | Ralph | Superpowers | GSD-2 |
|------|-------|-------------|-------|
| Fresh context granularity | story 단위 | task 단위 | milestone 단위 |
| Memory mechanism | 텍스트 파일 | skill files | SQLite DB |
| Orchestration | Bash script | Skill dispatch | Pi SDK |
| Scope | 한 사람이 돌아가며 | 팀 협업 | 엔터프라이즈 |

## Story Sizing (가장 중요한 규칙)

### Right-Sized Stories

```
✅ Good:
- "Add a database column and migration"
- "Add a UI component to an existing page"  
- "Update a server action with new logic"
- "Add a filter dropdown to a list"
```

### Too Big (Split these!)

```
❌ Bad:
- "Build the entire dashboard"
- "Add authentication"
- "Refactor the API"
```

> "각 PRD 항목은 한 context window 내에서 완료 가능해야 한다.  
> If a task is too big, the LLM runs out of context before finishing  
> and produces poor code."

## AGENTS.md 업데이트 (핵심 관행)

Ralph는 각 iteration 후 **AGENTS.md 파일을 업데이트**한다.

```bash
# Ralph가 수행하는 것
1. 편집한 디렉토리 identify
2. 해당 디렉토리/부모 디렉토리의 AGENTS.md 확인
3. 재사용 가능한 학습 내용 추가

# Example AGENTS.md addition:
## Auth Module Patterns
- When modifying LoginForm.tsx, also update ValidationRules.ts
- This module uses `useAuth()` hook for all auth state
- Tests require mock server running on port 3001
```

### 왜 중요한가?

> "AI coding tools automatically read these files, so future iterations  
> (and future human developers) benefit from discovered patterns"

즉, **AI 에이전트가 자동으로 AGENTS.md를 읽는다** → 다음 iteration에서 이전 학습 활용

## Quality Gates

```
모든 commit 전 통과해야 함:
┌─────────────────────────────────────┐
│                                     │
│  1. Typecheck (TypeScript/Python)   │
│  2. Lint (ESLint/Ruff)              │
│  3. Tests (passing)                 │
│                                     │
│  → 모두 통과해야 commit             │
│  → broken code은 compounding됨      │
│                                     │
└─────────────────────────────────────┘
```

## Browser Verification (Frontend Stories)

```bash
# Frontend story에는 반드시 browser verification 포함
1. Load dev-browser skill
2. Navigate to relevant page
3. Verify UI changes work
4. Take screenshot for progress log

# Ralph의 규칙:
"A frontend story is NOT complete 
 until browser verification passes."
```

## Ralph Flowchart

```
[Start] → [Read prd.json] → [Read progress.txt]
   ↓
[Pick highest priority story where passes: false]
   ↓
[Create/checkout branch from prd.branchName]
   ↓
[Implement ONE story]
   ↓
[Run quality checks]
   ↓
[Update AGENTS.md with patterns]
   ↓
[Commit if checks pass]
   ↓
[Update prd.json → passes: true]
   ↓
[Append to progress.txt]
   ↓
[All stories pass?] ──yes──→ [<promise>COMPLETE</promise>]
   │ no
   └──→ [Repeat]
```

## Ralph.sh 분석

```bash
#!/bin/bash
# ralph.sh 핵심 구조

TOOL=${1:-amp}  # amp or claude
MAX_ITER=${2:-10}

for i in $(seq 1 $MAX_ITER); do
  # 1. Spawn fresh instance
  $TOOL --prompt "$(cat prompt.md)"
  
  # 2. Check if complete
  if grep -q "<promise>COMPLETE</promise>" progress.txt; then
    echo "All stories complete!"
    break
  fi
done
```

### prompt.md의 역할

```markdown
# prompt.md는 매 iteration마다 로드됨
# 이 안에는:
# 1. Task 선택 로직
# 2. Quality check 명령어
# 3. prd.json 업데이트 방법
# 4. progress.txt append 형식
# 5. AGENTS.md 업데이트 규칙
# 6. Browser verification 방법
```

## 초보자를 위한 인사이트

### Ralph가 가르치는 것

```
[Ralph 사고방식]
1. "작은 작업으로 분해하라" → story 단위 작게
2. "context는 비용이다" → fresh context per iteration
3. "기억은 파일에 저장하라" → prd.json, progress.txt, AGENTS.md
4. "품질 게이트는 필수다" → typecheck + test + lint
5. "완료 조건을 명확히하라" → passes: true/false로 상태 추적
```

### [[engineering-thinking-skill]]에 대한 시사점

| Ralph 패턴 | Engineering Thinking 적용 |
|-----------|--------------------------|
| Story sizing | Decompose: tractable chunks 분해 |
| Fresh context per iteration | Fresh context 전략 |
| prd.json (passes state) | 마일스톤 완료 조건 명시 |
| progress.txt (learnings) | Session log / discovery notes |
| AGENTS.md patterns | ADR 문서화 + wiki 연동 |
| Quality gates | Verification criteria |

## [[context-rot]]과의 관계

Ralph는 [[context-rot]]을 **사전에 방지**하는 설계:

```
Context Rot이 발생하는 이유:
- 토큰 누적 → 비선형 성능 저하

Ralph의 해결책:
- iteration마다 fresh context (토큰 0)
- 누적 데이터는 파일로 분리
- 새 iteration이 파일만 읽으면 됨
```

## Similar Projects

- [[gstack]] — Role-based skill system
- [[gsd-2]] — Actual agent control with Pi SDK
- [[superpowers]] — TDD-based workflow

## Tags

#fresh-context #loop-pattern #prd-driven #story-sizing #quality-gates #context-rot
