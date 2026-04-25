---
title: Decomposition
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [decomposition, agentic-coding, planning]
sources: [raw/articles/anthropic-engineering-harness-design-long-running-apps.md, raw/articles/anthropic-engineering-building-effective-agents.md]
confidence: high
---

# Decomposition

## Definition

Decomposition is the practice of breaking complex, multi-step tasks into smaller, tractable units that can be executed independently with verifiable outcomes.

## Why Decomposition Matters

For AI agents:
1. **Each unit fits in a fresh context** — avoids [[context-rot]]
2. **Failures are contained** — one bad unit doesn't corrupt everything
3. **Progress is measurable** — each unit has a clear definition of done
4. **Recovery is possible** — can restart from last successful unit

## Decomposition Heuristics

### Ralph's "One Context Window" Rule
> A story must be completable within one fresh context window. If it's too large, break it down.

### Gsd-2's Task Hierarchy
```
Milestone
  └── Slice (feature area)
        └── Task (atomic unit)
              └── [fresh context per task]
```

### Anthropic's Approach
From "Building Effective Agents":
- Use an **orchestrator** agent to decompose the task
- The orchestrator plans sub-tasks
- Specialized agents execute sub-tasks
- The orchestrator Synthesizes results

## Rules for Good Decomposition

1. **Atomic outcomes** — each unit produces something verifiable
2. **Appropriate size** — fits in fresh context with room for verification
3. **Clear dependencies** — units can execute in dependency order
4. **Independent enough** — units can fail without cascading

## When Decomposition Goes Wrong

| Symptom | Cause | Fix |
|---------|-------|-----|
| Unit too large | Context overflow, slow execution | Split in two |
| Unit too small | Excessive coordination overhead | Merge with adjacent |
| Unclear dependencies | Integration failures | Draw dependency graph first |
| Fragile units | Changes cascade | Increase cohesion within unit |

## Related

- [[fresh-context]] — why size limits matter
- [[harness-design]] — harness patterns for decomposition
- [[skill-routing]] — routing to appropriate skills per unit