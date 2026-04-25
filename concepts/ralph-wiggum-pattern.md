---
title: Ralph Wiggum Pattern
created: 2026-04-24
updated: 2026-04-24
type: concept
tags: [coding-agents, automation, bash-loop, iterative-development]
sources: [raw/articles/ralph-wiggum-pattern.md]
confidence: high
---
# Ralph Wiggum Pattern

The **Ralph Wiggum Pattern** is a technique for autonomous software development that utilizes a simple continuous loop (often a bash `while` loop) to drive an AI coding agent (like Claude Code) through iterative cycles of implementation and verification.

## Core Principles
1. **One Item Per Loop**: The agent is instructed to pick the single most important task and implement it. This prevents scope creep and keeps the context window focused.
2. **Backpressure**: The loop includes an automated verification step (tests, linters, compilers). If the verification fails, the code is rejected, and the next loop attempts to fix it.
3. **Monolithic State**: Unlike complex multi-agent systems, Ralph is a single process. State is carried over through "artifacts" like `@fix_plan.md` (the roadmap) and `@AGENT.md` (the operational manual).
4. **Subagent Offloading**: Expensive tasks like code search (`ripgrep`) or summarizing logs are offloaded to subagents to preserve the primary context window for reasoning.
5. **Eventual Consistency**: The philosophy that any problem created by AI can be resolved through more loops and improved prompts/specifications.

## Comparison to Other Harnesses
While the [[harness-pattern]] (Anthropic) uses a structured three-agent system (Planner, Generator, Evaluator), Ralph is a more "monolithic" and "primitive" version that relies on high-frequency iteration and raw "backpressure" from the local development environment.

[[coding-agents]], [[harness-pattern]], [[context-engineering]].

^[raw/articles/ralph-wiggum-pattern.md]
