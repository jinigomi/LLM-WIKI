---
name: HermesBrain
description: "AI 협업 스킬 — 바이브코딩에서 기억하는 개발로"
version: 1.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [ai-collaboration, vibe-coding, memory-driven, mcp, orchestration, engineering-thinking]
    category: engineering
---

# Wiki Schema

## Domain
AI 협업 스킬 개발 — "[[기억하는 개발]]" 패턴, Claude 오케스트레이션, Obsidian 기반 프로젝트 기억 시스템

## Conventions
- File names: lowercase, hyphens, no spaces (e.g., `remembering-development.md`, `mcp-protocol.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers:** On pages that synthesize 3+ sources, append `^[raw/articles/source-file.md]`

## Frontmatter
```yaml
---
title: Page Title
created: 2026-04-25
updated: 2026-04-25
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
confidence: high | medium | low
contested: true
contradictions: [other-page-slug]
---
```

## Tag Taxonomy
- **Core Concepts**: 기억하는-개발, 엔지니어링-사고, 오케스트레이션, 바이브코딩, fresh-context, context-rot
- **Roles/Tools**: claude-desktop, claude-code, obsidian, mcp
- **Patterns**: 역할-분배, decomposition, 에러-에스컬레이션, 설계-우선, 기록-습관
- **Skills**: 초보자-스킬, gstack, gsd, superpowers, ralph
- **Meta**: comparison, limitation, workflow, best-practice

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
