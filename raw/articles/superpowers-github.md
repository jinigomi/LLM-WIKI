---
source_url: https://github.com/obra/superpowers
ingested: 2026-04-29
sha256: b4f8d1c9a2e5f3b7d6c8a1e4f5d7c9b2a3e6f8d1c4b7a9e2f5d8c1a4b7e9f2d5c
---

# Superpowers — GitHub Repository Analysis

**Repo:** obra/superpowers v5.0.7
**Analyzed:** 2026-04-29

## Structure

```
superpowers/
├── skills/          ← 13 skills (핵심)
├── agents/          ← code-reviewer subagent
├── hooks/           ← SessionStart → using-superpowers
├── commands/        ← deprecated (skills로 대체)
├── docs/            ← plans/, specs/
├── CLAUDE.md        ← Claude Code용 instruction
├── GEMINI.md        ← Gemini CLI용 instruction
├── AGENTS.md        ← Codex/Aider용 instruction
├── COPILOT.md       ← Copilot CLI용 instruction
└── README.md
```

## Skills

1. using-superpowers (entrypoint)
2. brainstorming → writing-plans → subagent-driven-dev (core pipeline)
3. executing-plans (single session)
4. test-driven-development (RED-GREEN-REFACTOR)
5. systematic-debugging (4-phase root cause)
6. requesting-code-review → receiving-code-review
7. verification-before-completion
8. finishing-a-development-branch
9. using-git-worktrees
10. dispatching-parallel-agents
11. writing-skills (TDD for docs)
12. dispatching-parallel-agents

## Key Design Decisions

- Commands deprecated in favor of skills
- SessionStart hook auto-loads using-superpowers
- Subagent isolation: no session context inheritance
- Two-stage review: spec compliance → code quality
- "Human partner" terminology throughout
- Iron Law pattern: non-negotiable rules
- Rationalization defense built into TDD/verification skills