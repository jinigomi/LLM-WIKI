# Wiki Schema

## Domain
AI Agent Engineering — Claude, LLM coding agents, harness design, context engineering, and agentic AI development best practices.

## Conventions
- File names: lowercase, hyphens, no spaces (e.g., `context-rot.md`, `harness-design.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers:** On pages that synthesize 3+ sources, append `^[raw/articles/source-file.md]` at the end of paragraphs whose claims come from a specific source

## Frontmatter
```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
confidence: high | medium | low
contested: true
contradictions: [other-page-slug]
---
```

## Tag Taxonomy
- **Techniques**: context-engineering, harness-design, agentic-coding, tool-use, multi-agent, evaluation, tdd
- **Concepts**: context-rot, fresh-context, checkpointing, self-evaluation, skill-routing, decomposition
- **Meta**: comparison, timeline, controversy, prediction, best-practice
- **Tools/Products**: claude-code, mcp, anthropic
- **Research**:SWE-bench, niah, long-context

## Page Thresholds
- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions, minor details
- **Split a page** when it exceeds ~200 lines

## Entity Pages
One page per notable entity. Include: Overview, Key facts, Relationships ([[wikilinks]]), Source references.

## Concept Pages
One page per concept. Include: Definition, Current state, Open questions, Related concepts ([[wikilinks]]).

## Update Policy
When new information conflicts: check dates → note both positions → mark `contradictions:` → flag for review.

## Wiki for Engineering Thinking Skill
This wiki also documents the "engineering-thinking-beginner" skill being developed — see `concepts/engineering-thinking-skill.md`.