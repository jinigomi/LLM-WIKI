---
title: Harness Design
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [harness-design, agentic-coding, multi-agent]
sources: [raw/articles/anthropic-engineering-harness-design-long-running-apps.md]
confidence: high
---

# Harness Design

## Definition

Harness design is the engineering of the system surrounding an AI agent — how it receives tasks, how it executes them, how context is managed, and how results are verified. It determines whether agents succeed on long-running, complex tasks.

## Core Problem

Two persistent failure modes in long-running agents:

1. **Context window saturation** — models lose coherence as context fills
2. **Self-evaluation failure** — agents praise their own work even when quality is poor

## Key Patterns

### 1. Context Reset (not just compaction)

Anthropic found that **compaction alone doesn't work**. Claude Sonnet 4.5 exhibited strong "context anxiety" — wrapping up work prematurely near limits.

**Solution**: Full context reset with structured handoff artifact.

```
┌─────────────────────────────────────────────┐
│ Agent 1: Work → Generate Handoff Artifact   │
│   - What was accomplished                   │
│   - What's in flight                        │
│   - What needs to happen next               │
│   - Critical context for next agent         │
└─────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────┐
│ Agent 2 (fresh): Read Handoff → Continue    │
└─────────────────────────────────────────────┘
```

### 2. Generator / Evaluator Separation

Inspired by GANs — separate the agent doing work from the agent judging it.

- **Problem**: Agents grading their own work are too lenient
- **Solution**: Standalone evaluator agent that grades against concrete criteria
- **Tuning the evaluator**: Make it skeptical. "Does this follow principles?" is easier than "Is this good?"

### 3. Three-Agent Architecture (Anthropic's final design)

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Planner    │────▶│  Generator  │────▶│  Evaluator  │
│ (decompose) │     │   (build)   │     │  (grade)    │
└─────────────┘     └─────────────┘     └─────────────┘
       │                                       │
       │         iteration ◀───────────────────┘
       │
       └──────────────────────────────────────▶ (back to planner if needed)
```

## Decomposition Principle

Break builds into tractable chunks. Each chunk should:
- Be completable in one fresh context window
- Have verifiable output
- Be small enough to fail fast and recover

## See Also

- [[context-rot]] — why context management matters
- [[fresh-context]] — the strategic approach to context
- [[self-evaluation]] — the generator/evaluator problem
- [[decomposition]] — breaking work into tractable units