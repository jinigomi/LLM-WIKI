---
name: HermesBrain
description: "LLM 협업을 위한 영구 컴파운딩 Wiki — 카파시 LLM Wiki 패턴 충실 구현"
version: 2.0
author: Hermes Agent
license: MIT
---

# Wiki Schema

## Domain
LLM 협업 스킬 개발 — AI 에이전트(Hermes)와 함께 쌓는 개인 지식 베이스.
"기억하는 개발" 패턴: 매 세션의 맥락을 Obsidian에 영구 저장, 누적.

## Architecture
```
wiki/
├── SCHEMA.md          # 이 파일 — 구조 규칙, 태그 택소노미
├── index.md           # 콘텐츠 카탈로그 — 모든 페이지를 类型별로 요약
├── log.md             # 시계열 액션 로그 (append-only)
├── raw/               # Layer 1: 불변 원본 소스
│   ├── articles/      # 웹 기사 (완료분)
│   ├── papers/        # 논문, 기술 문서
│   ├── transcripts/   # 세션 로그, 미팅 노트
│   └── sessions/      # Hermes 세션 덤프
├── entities/          # Layer 2: 사람, 조직, 제품, 프로젝트
├── concepts/          # Layer 2: 개념, 주제, 기법
├── comparisons/       # Layer 2: 대비 분석
└── queries/           # Layer 2:值钱한 Q&A 결과
```

Layer 1 (raw/)은 불변. Layer 2 (entities/, concepts/ 등)는 에이전트가 소유.
Layer 3 (SCHEMA, index, log)은 메타.

## Conventions

- File names: lowercase, hyphens, no spaces (e.g., `contextual-retrieval.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **raw/는 절대 수정하지 말 것** — 수정이 필요하면 wiki page에서 처리

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

- **AI/LLM**: model, architecture, training, fine-tuning, inference, alignment, reasoning
- **Evaluation**: benchmark, eval, harness, swe-bench, postmortem
- **Collaboration**: orchestration, agent, multi-agent, context-engineering, tool-use
- **Tools/infra**: mcp, claude-code, obsidian, hermes, rss, cron
- **Development**: engineering-thinking, vibe-coding, error-escalation, memory-driven
- **People/Orgs**: person, company, lab, open-source
- **Meta**: comparison, timeline, controversy, prediction, limitation
- **Domain**: reading, research, project, session
- **Platform**: telegram, discord, delivery, bot
- **Techniques**: rag, embedding

**Rule:** 새 태그 사용 전 반드시 여기서 먼저 추가.

## Page Thresholds

- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions, minor details
- **Split a page** when it exceeds ~200 lines — break into sub-topics with cross-links
- **Archive a page** when content is fully superseded — move to `_archive/`, remove from index

## Entity Pages

One page per notable entity. Include:
- Overview / what it is
- Key facts and dates
- Relationships to other entities ([[wikilinks]])
- Source references

## Concept Pages

One page per concept or topic. Include:
- Definition / explanation
- Current state of knowledge
- Open questions or debates
- Related concepts ([[wikilinks]])

## Update Policy

When new information conflicts with existing content:
1. Check the dates — newer sources generally supersede older ones
2. If genuinely contradictory, note both positions with dates and sources
3. Mark the contradiction in frontmatter: `contradictions: [page-name]`
4. Flag for user review in the lint report

## raw/ Frontmatter

```yaml
---
source_url: https://example.com/article
ingested: YYYY-MM-DD
sha256: <hex digest of body>
---
```

`sha256`으로 재수집 시 동일하면 스킵, 다르면 drift 플래그.