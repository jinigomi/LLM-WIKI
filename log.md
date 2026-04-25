# Wiki Log

> Chronological record. Append-only.

## [2026-04-25] create | Wiki initialized
- Domain: AI Agent Engineering (Claude, LLM coding agents, harness design, context engineering)
- Structure: SCHEMA.md, index.md, log.md, raw/, entities/, concepts/, comparisons/, queries/

## [2026-04-25] ingest | Anthropic Engineering blog posts (partial)
- Fetched: Harness Design (full), Building Effective Agents (partial)

## [2026-04-25] analyze | 4 open-source AI agent projects (cloned + deep-dive)
- gstack: 19 role-based skills, template-driven SKILL.md generation, safety/freeze system, preamble-based context injection
- gsd-2: Pi SDK-based actual agent control, Context Mode, memories DB (ADR-013), 8 specialist subagents, 5-wave state machine, tiered context injection (65%+ token reduction)
- superpowers: TDD-based methodology, RED-GREEN-REFACTOR enforced, 94% PR rejection awareness, two-stage review (spec compliance + code quality), subagent-driven development
- ralph: Fresh context per iteration, PRD-driven story loop, progress.txt append-only learnings, AGENTS.md pattern updates, quality gates
- Sources: raw/articles/{gstack,gsd2,superpowers,ralph}-{readme,agents,skill,claude,prompt}.md
- Wiki pages: concepts/{gstack,gsd-2,superpowers,ralph}.md (each ~150-200 lines)
- Not fetched: Other posts due to URL changes / bot detection
- Created concept pages: context-rot, fresh-context, harness-design, self-evaluation, decomposition, skill-routing, engineering-thinking-skill
- Total wiki pages: 8

## [2026-04-25] create | Engineering Thinking Skill (WIP)
- Location: ~/.hermes/skills/engineering-thinking-beginner/SKILL.md
- Templates: PRD, session state, routing rules
- Influences: gstack, gsd-2, superpowers, ralph, Chroma context rot, Anthropic harness design