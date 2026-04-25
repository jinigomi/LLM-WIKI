---
title: Context Rot
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [context-engineering, long-context, research]
sources: [raw/articles/chroma-context-rot-research.md]
confidence: high
---

# Context Rot

## Definition

Context rot refers to the degradation of LLM performance as input context length increases. Even on simple tasks, models become increasingly unreliable as more tokens are added to the context.

## Key Findings

### From Chroma Research (July 2025)

- **Performance degrades non-linearly** with token count
- Models don't process context uniformly — early tokens are privileged
- Simple retrieval tasks (Needle in a Haystack) show near-perfect scores, but:
  - Semantic matching tasks: significant degradation at 50k+ tokens
  - Repetitive tasks: severe degradation even earlier
  - Multi-hop reasoning: compounding failures

### From Anthropic Engineering

- Claude Sonnet 4.5 exhibited **"context anxiety"** — wrapping up work prematurely near context limits
- Compaction (summarizing history in place) doesn't solve context anxiety
- **Context reset** (fresh agent + structured handoff) is the only reliable solution
- Reset gives a clean slate at cost of: orchestration complexity, token overhead, latency

## The Compaction vs Reset Distinction

| Approach | What it does | Works for context anxiety? |
|----------|--------------|---------------------------|
| **Compaction** | Summarizes earlier context in place, same agent continues | No — agent still "feels" the limit |
| **Reset** | Clears context window, starts fresh agent with handoff artifact | Yes — clean slate |

## Practical Implications

1. **Don't max out context** — performance degrades before you hit the limit
2. **Checkpoint before 50k tokens** — start fresh context rotation
3. **Structured handoff artifacts** — must contain enough state for next agent to continue
4. **Monitor for context anxiety** — agent wrapping up early = sign to reset

## Related Concepts

- [[fresh-context]] — strategy of starting with clean state per unit
- [[checkpointing]] — periodic state saves to enable recovery
- [[harness-design]] — system-level patterns for long-running agents