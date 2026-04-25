---
title: Fresh Context
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [fresh-context, context-engineering, checkpointing]
sources: [raw/articles/anthropic-engineering-harness-design-long-running-apps.md, raw/articles/anthropic-engineering-scaling-managed-agents.md]
confidence: high
---

# Fresh Context

## Definition

Fresh context is the strategy of starting each unit of work (milestone, task, or iteration) with a clean context window — no accumulated history, no degraded performance from long context.

## Why It Matters

From [[context-rot]]: LLM performance degrades non-linearly as context length increases. Starting fresh means:
- No performance degradation from context length
- No "context anxiety" — agent doesn't feel pressured to wrap up
- Clean slate for focused work

## Implementation Patterns

### 1. Milestone-Based Fresh Context (gsd-2, ralph)

Each milestone = one fresh context. At milestone boundary:
1. Generate handoff artifact summarizing previous work
2. Start new agent with handoff artifact as only context
3. Continue from where previous left off

### 2. Ralph's "One Context Window" Rule

```
Story size constraint: "one context window에서 완료 가능한 규모만 허용"
- Too large = broken code产出
- Too small = inefficiency
- Just right = completable with fresh context
```

### 3. Anthropic's Context Reset

Full reset (not compaction) when:
- Context reaches ~50k tokens
- Agent shows signs of wrapping up prematurely (context anxiety)
- Task requires different focus than original prompt

### 4. Gsd-2's State-Based Recovery

- `.gsd/STATE.md` — file-based state engine
- Reads state to decide next action
- Enables crash recovery with fresh context

## Handoff Artifact Structure

A good handoff artifact must answer:

```
## What was accomplished
{summary of completed work}

## What's in flight
{current work}

## What needs to happen next
{next steps}

## Important context for next agent
{any caveats, decisions, partial implementations}
```

## Trade-offs

| Fresh Context | Cost |
|--------------|------|
| Pros | Clean slate, no degradation, full agent attention |
| Cons | Token overhead for handoff, orchestration complexity, slight latency |

## Related

- [[context-rot]] — the problem fresh context solves
- [[checkpointing]] — when to create fresh context points
- [[harness-design]] — how to orchestrate fresh contexts
- [[decomposition]] — sizing units for fresh context