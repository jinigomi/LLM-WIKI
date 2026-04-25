---
source_url: https://ghuntley.com/ralph/
ingested: 2026-04-24
author: Geoffrey Huntley
title: Ralph Wiggum as a "software engineer"
---

# Ralph Wiggum as a "software engineer"

Ralph is a technique. In its purest form, Ralph is a Bash loop:
`while :; do cat PROMPT.md | claude-code ; done`

## Core Concepts
- **One Item Per Loop**: Focus the agent on exactly one task per iteration.
- **Backpressure**: Use tests, static analyzers, and compilers to reject invalid code.
- **Monolithic Architecture**: Ralph works autonomously in a single repository as a single process.
- **Deterministic Stack**: Allocate the plan (`@fix_plan.md`) and specifications to the context window every loop.
- **Subagents**: Use subagents for searching and summarization to keep the primary context window small (~170k).
- **Eventual Consistency**: Trust that repeated loops and "tuning" (updating prompts/specs) will resolve defects.
- **AGENT.md & fix_plan.md**: Files used to maintain state and instructions across loops.

## The Loop
1. **Generate**: Use specifications and technical standard libraries to steer code generation.
2. **Backpressure**: Run tests/analyzers. Reject if they fail.
3. **Capture & Learn**: Update `@AGENT.md` with new learnings and `@fix_plan.md` with bugs or next steps.

^[raw/articles/ralph-wiggum-pattern.md]
