# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

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