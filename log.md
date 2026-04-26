# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-04-26] lint | Wiki lint performed — 11 checks, all errors fixed

### Errors fixed
- **Broken wikilinks**: 34개 수정 (한국어 스페이스, concepts/ prefix, .txt 파일 링크)
- **Missing frontmatter**: 5개 entity 파일에 frontmatter 추가 (설계, 2026-04-26, 프로젝트-개요, 챕터-요약, 체크리스트)
- **Tag taxonomy**: 40+ 개 태그를 유효한 택소노미로 매핑
- **context-rot page**: [[context-rot]] 링크 대상이었던 entities/context-rot.md 생성

### Remaining warnings (acceptable)
- 7개 orphan pages (프로젝트 개요 문서들, 자연스러운 상황)
- 4개 pages with 0 outbound wikilinks (체크리스트, 세션 로그 등 — content 제한)
- 설계 234줄 (project page, splitting deferred)

### Added to taxonomy
- `evaluation`, `platform` (telegram, discord, delivery, bot), `rag`, `embedding`

## [2026-04-26] restructure | Wiki reorganized to Karpathy LLM Wiki pattern

### What changed
- Removed `Project/` folder — moved to `entities/` (projects are entities)
- Reorganized `concepts/` — only 6 pages with real content remain (22 stubs moved to `raw/archived-stubs/`)
- Created `queries/` and `comparisons/` directories (empty, for future use)
- Restructured `raw/` to proper subdirs: `articles/`, `papers/`, `transcripts/`, `sessions/`
- Moved completed Anthropic articles to `raw/articles/` (full content preserved)
- Updated `SCHEMA.md` to v2.0 — aligned with Karpathy LLM Wiki pattern
- Updated `index.md` — accurate listing of all 27 wiki pages

### File movements
- `Project/*` → `entities/`
- `concepts/anthropic-*.md` (stubs) → `raw/archived-stubs/`
- `concepts/anthropic-*-*-mcp.md` (full) → `raw/articles/`
- `concepts/context-rot.md` → `raw/articles/`

### Current structure
```
HermesBrain/
├── SCHEMA.md, index.md, log.md
├── concepts/     — 6 pages (real content only)
├── entities/     — 12 pages (projects, practices, sessions)
├── comparisons/  — empty
├── queries/      — empty
└── raw/
    ├── articles/     — 8 complete articles (Anthropic, TryChroma)
    ├── archived-stubs/ — 19 stub files (needs future content)
    ├── papers/       — 1 paper
    ├── transcripts/  — 6 session logs
    └── sessions/     — (session dumps)
```

## [2026-04-25] create | Wiki initialized
- Domain: AI 협업 스킬 개발 — "기억하는 개발" 패턴
- Initial structure: SCHEMA.md, index.md, log.md, concepts/, entities/, raw/