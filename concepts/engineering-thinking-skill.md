---
title: Engineering Thinking Skill
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [skill, engineering-thinking, beginner-friendly, tdd, ddd]
sources: []
confidence: medium
contested: true
---

# Engineering Thinking Skill

> ⚠️ This page documents a skill **under development**. Concepts may change as we learn from testing.

## Purpose

A beginner-friendly skill that simulates 30 years of senior full-stack developer's engineering thinking. Designed for use with AI coding agents.

## Problem It Solves

- **Beginners can't verify AI output** — they accept everything AI generates
- **Beginners can't define success** — vague goals produce vague results
- **Beginners don't think in layers** — they jump to code without architecture
- **Beginners don't plan for failure** — no debugging strategy, no exit criteria

## Core Principles

1. **AI amplifies coding skill, not thinking ability** — a senior with AI is still a senior; a beginner with AI is just a faster beginner

2. **Ask questions, don't give lectures** — TDD/DDD/Clean Architecture are渗透 through questions, not technical demands

3. **Context rotation prevention is critical** — for multi-turn AI sessions, context must be managed

## Five Phases

```
┌─────────────────────────────────────────────────────────────────────┐
│  Phase 1: FRAMING (구체화)                                          │
│  9 questions to turn vague idea → structured PRD                    │
│  WHAT / WHO / WHY / WHEN / BUDGET / SUCCESS / RISK / EXIT / DEBUG   │
│                                                                     │
│  Phase 2: ROUTING (스킬 선택)                                       │
│  Match PRD characteristics → appropriate skill chain                │
│  Purpose-based, constraint-based, risk-based routing                │
│                                                                     │
│  Phase 3: EXECUTION (실행)                                          │
│  Fresh context per milestone, file-based state, checkpointing       │
│                                                                     │
│  Phase 4: VERIFICATION (검증)                                       │
│  TDD questions before implementation:                               │
│  "How will you test this?" / "What must fail?" / "How do you know?" │
│                                                                     │
│  Phase 5: DEBUGGING (디버깅)                                        │
│  5-step protocol when bugs are found                                │
└─────────────────────────────────────────────────────────────────────┘
```

## Key Influences

| Source | Contribution |
|--------|-------------|
| [[gstack]] | Skill routing via CLAUDE.md |
| [[gsd-2]] | Fresh context per task, file-based state |
| [[superpowers]] | TDD for skill writing ("NO SKILL WITHOUT FAILING TEST") |
| [[ralph]] | "One context window" story size constraint |
| [[context-rot]] (Chroma) | Context degradation evidence |
| [[harness-design]] (Anthropic) | Generator/evaluator separation |

## Current Status

✅ SKILL.md created at `~/.hermes/skills/engineering-thinking-beginner/SKILL.md`

✅ Templates created:
- PRD template
- Session state template
- Routing rules

⏳ Testing pending — need complex project to test against

## Open Questions

1. **Granularity** — are 9 framing questions too many for beginners?
2. **Automated routing** — can routing be automatic or needs human input?
3. **Context rot integration** — how to integrate context monitoring into skill itself?
4. **Evaluator separation** — should skill include a built-in evaluator?

## Related

- [[fresh-context]] — how to implement context rotation
- [[harness-design]] — overall system design
- [[self-evaluation]] — generator/evaluator pattern
- [[decomposition]] — breaking work for context limits