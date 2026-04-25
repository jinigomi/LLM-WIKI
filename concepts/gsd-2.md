# GSD 2 — Get Shit Done

> **URL**: https://github.com/gsd-build/gsd-2  
> **크기**: 거대 (README 59KB, CHANGELOG 201KB)  
> **핵심**: Pi SDK 기반 실제 에이전트 제어 시스템

## 개요

GSD v1은 markdown 프롬프트 컬렉션이었다. AI가 프롬프트를 읽고 올바르게 행동하기를 바랐다.  
GSD v2는 **TypeScript 애플리케이션** — 에이전트 세션을 **실제 제어**한다.

```
v1의 한계                    v2의 해결
─────────────────────────────────────────────────
No context control     →    Tiered Context Injection (M005)
No real automation     →    Pi SDK 직접 제어
No crash recovery      →    DB-authoritative state
No observability       →    Cost tracking, dashboard
```

## 핵심 아키텍처

### 1. Pi SDK Integration

```typescript
// GSD는 Pi SDK 기반
// LLM이 아닌 앱이 에이전트 직접 제어
const agent = new PiAgent({
  model: 'claude-sonnet-4',
  contextWindow: 200000
})
// → clear context between tasks
// → inject files at dispatch time
// → manage git branches
// → track cost and tokens
```

### 2. Context Mode (v2.77+)

```yaml
# .gsd/config.yaml
context_mode:
  enabled: true  # new projects default
  pull:
    - project_artifacts
    - prior_session_state
    - milestone_signals
    - execution_metadata
```

v2.77에서 Context Mode는:
- Task-ready context 자동 구축
- Relevant artifacts 자동 수집
- 첫 디스패치부터 올바른 파일/제약 조건 제공

### 3. Memory Architecture (ADR-013)

```
v2.77 이전: dual-write migration
v2.77 이후: memories table이 단일 진실 공급원
```

```sql
-- Structured memory fields
CREATE TABLE memories (
  id TEXT PRIMARY KEY,
  content TEXT,
  structured_fields JSON,  -- machine-usable metadata
  agent_id TEXT,
  created_at TIMESTAMP
);
```

이전:
- memories + legacy_memories 분산
- reconciliation edge cases 많음

이후:
- 단일 경로로 모든 memory read/write
- fewer sync drift

### 4. Knowledge Graph System (v2.75+)

```bash
/gsd extract-learnings
# → decisions, lessons, patterns, surprises 추출
# → LEARNINGS.md 생성
```

프로젝트 아티팩트에서 구조화된 knowledge graph 구축.

### 5. 8 Specialist Subagents (v2.72+)

```yaml
# capability-aware routing (ADR-004)
specialists:
  - planning_agent      # requirements gathering
  - architecture_agent  # system design
  - implementation_agent # coding
  - test_agent          # testing
  - review_agent        # quality assurance
  - debug_agent         # troubleshooting
  - deployment_agent    # shipping
  - cleanup_agent       # debt management
```

### 6. Worktree Isolation

```bash
# git worktree per milestone
git worktree add ../milestone-2 feature/milestone-2

# 각 마일스톤 격리된 환경에서 실행
```

### 7. Unified Orchestration Kernel (UOK)

v2.75+: 기본 실행 경로
- plan-v2 compile gates
- Reactive/parallel scheduling
- 5-wave state machine

## State Machine (5-Wave)

```
discuss → plan → implement → verify → complete
   ↓         ↓        ↓         ↓
 (depth   (compile   (test    (milestone
  gates)    gates)   gates)    completion)
```

## Token Optimization

### Tiered Context Injection (M005)

```
Tier 1: 현재 태스크에만 직접 필요한 파일
Tier 2: 관련 모듈/의존성
Tier 3: 프로젝트 전체 컨텍스트

→ 65%+ token reduction
```

### Complexity-Based Model Selection

```yaml
# .gsd/config.yaml
model_routing:
  simple_task: claude-haiku
  standard_task: claude-sonnet-4
  complex_task: claude-opus-4
```

## Fresh Context 전략

### DB-Authoritative State

```sql
-- milestone completion이 파일이 아닌 DB에서 파생
SELECT COUNT(*) FROM stories WHERE passes = true;
-- = milestone completion state
```

### Single-Writer DB Invariant

```typescript
// GSD의 state engine은 single-writer
// → DB corruption 방지
// → race condition 제거
```

### Stuck Detection

```typescript
// memory pressure watchdog
// stuck state가 세션 간 persiste (v2.73+)
interface StuckState {
  detectedAt: Date
  reason: string
  recoveryAttempts: number
}
```

## 초보자를 위한 인사이트

### GSD가 가르치는 것

```
[GSD 사고방식]
1. "프롬프트만으로는 부족하다" → 앱이 직접 제어해야 함
2. "context는 비용이다" → tiered injection으로 최적화
3. "state는 파일이 아닌 DB에" → single source of truth
4. "자동화는 단순 호출이 아니다" → crash recovery, stuck detection
5. "작업은 격리되어야 한다" → worktree per milestone
```

### [[engineering-thinking-skill]]에 대한 시사점

| GSD 패턴 | Engineering Thinking 적용 |
|---------|--------------------------|
| Context Mode | Framing 단계에서 Context Document 자동 구축 |
| memories table | Discovery 단계에서 질문/답변 기록 |
| Tiered injection | Decompose 단계에서 tiered requirement 작성 |
| 5-wave state machine | 전체 프로세스의 메타 프레임워크 |
| Single-writer DB | Spec 작성 시 단일 진실 공급원 원칙 |

## Similar Projects

- [[gstack]] — Role-based skill system
- [[superpowers]] — TDD 기반 워크플로우
- [[ralph]] — Fresh context loop

## Tags

#agent-control #context-optimization #state-management #pi-sdk #token-optimization
