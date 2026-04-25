---
title: Self-Evaluation Failure
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [self-evaluation, agentic-coding, evaluation]
sources: [raw/articles/anthropic-engineering-harness-design-long-running-apps.md]
confidence: high
---

# Self-Evaluation Failure

## The Problem

Agents evaluating their own work exhibit a systematic bias: they **over-rate the quality** of what they've produced. This happens even when quality is objectively mediocre.

Why? LLMs are trained to be helpful — to say yes, to complete requests, to generate confident responses. When an agent evaluates its own code/design, it's simultaneously:
1. The creator (wants to believe work is good)
2. The judge (should be objective)

## Evidence from Anthropic

From the Harness Design article:
- Absent intervention, Claude gravitate toward "safe, predictable" outputs that are technically functional but visually unremarkable
- When asked "is this good?", agents respond by confidently praising the work
- The problem is particularly pronounced for **subjective tasks** (design) but also affects **verifiable tasks** (agents make poor judgment calls)

## Solution: Generator/Evaluator Separation

Inspired by Generative Adversarial Networks (GANs):

```
┌─────────────────────────────────────────────────┐
│  GENERATOR (does the work)                      │
│  - Focused on building                          │
│  - Receives feedback from evaluator             │
│  - Iterates against external criteria           │
└─────────────────────────────────────────────────┘
                        ↓ feedback
┌─────────────────────────────────────────────────┐
│  EVALUATOR (judges the work)                    │
│  - Separate agent, not same agent               │
│  - Tuned to be skeptical                        │
│  - Grades against concrete criteria             │
└─────────────────────────────────────────────────┘
```

### Key Insight

Tuning a standalone evaluator to be skeptical is **far more tractable** than making a generator critical of its own work.

Once external feedback exists, the generator has something **concrete to iterate against**.

## Making Subjective Quality Gradable

The evaluator can't just say "is this beautiful?" — that's as hard as the original problem.

Instead, ask: **"Does this follow our principles for good design?"**

| Question | Gradable? |
|----------|-----------|
| "Is this beautiful?" | No — subjective |
| "Does this follow our design principles?" | Yes — check against criteria |
| "Are there accessibility issues?" | Yes — check WCAG criteria |
| "Is the code maintainable?" | Partially — can check specific metrics |

## Related

- [[harness-design]] — where self-evaluation failure is addressed
- [[evaluation]] — broader topic of eval design
- [[multi-agent]] — generator/evaluator as two-agent system