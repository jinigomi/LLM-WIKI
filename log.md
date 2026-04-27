# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-04-27] create | Research Agent 설계 계획
- **문서**: `entities/research-agent-설계-계획.md`
- **내용**: 연구 에이전트 도구 분류, Design Patterns, 6단계 워크플로우, 구현 태스크 5개
- **인사이트**: 사용자 제공 "연구 전문가 스킬" 요구사항 기반
- **다음**: writing-plans → subagent-driven-development 순서로 실행

## [2026-04-27] create | HyperAgents Meta Agent 분석

- **문서**: `docs/hyperagents-meta-agent-analysis.md`
- **내용**: 초보자→고품질 결과물을 위한 HyperAgents 적용 설계
- **핵심**: 3단계 품질 게이트 (Task Agent → Meta Agent Review → Metacognitive Check)
- **스킬 3개 포함**: novice-quality-gate, metacognitive-scaffold, senior-criteria-archive
- **목표**: 초보자의 아이디어가 Senior Developer 수준 결과물로 인증받는 시스템

## [2026-04-27] ingest | wikidocs '하네스 엔지니어링 입문' 스크래핑

- **來源**: https://wikidocs.net/book/19559
- **수집**: 10/13 챕터 (3.5, 4, 5장은 '작성중' 상태)
- **파일**: `raw/books/하네스-엔지니어링-입문-wikidocs.md`
- **요약**: `entities/harness-engineering-wikidocs.md`
- **분석**: `entities/harness-engineering-wikidocs.md` 심층 분석 섹션
- **보고서**: `/Users/bricoleur/secall/하네스-엔지니어링-심층-분석-보고서.md`

### Lint 수정
- `harness-engineering-wikidocs.md`: `updated` 필드 추가, frontmatter 오류 수정
- `raw/books/하네스-엔지니어링-입문-wikidocs.md`: raw frontmatter 형식 추가
- `index.md`: 2개 새 entity 등록, last updated 갱신, 총 페이지 20개

## [2026-04-26] lint | Wiki lint performed — 11 checks, all errors fixed

### Errors fixed
- **Broken wikilinks**: 34개 수정 (한국어 스페이스, concepts/ prefix, .txt 파일 링크)
- **Missing frontmatter**: 5개 entity 파일에 frontmatter 추가
- **Tag taxonomy**: 40+ 개 태그를 유효한 택소노미로 매핑
- **context-rot page**: [[context-rot]] 링크 대상이었던 entities/context-rot.md 생성
- **Concept dead-ends**: 2개 concept에 wikilink 추가 (anthropic-claude-think-tool, anthropic-contextual-retrieval)

### Remaining warnings (acceptable)
- entities/2026-04-26.md (세션 로그, 자연스러운 진입점)
- entities/체크리스트.md (체크리스트, 자연스러운 진입점)
- SCHEMA.md (스키마 문서, 자연스러운 진입점)

## [2026-04-26] integrate | Obsidian CLI + Wiki 통합 스킬 생성

- **New skill**: `obsidian-wiki-integration` (note-taking/)
- Obsidian CLI 분석 결과: 68 files, 49 orphans, 2 unresolved links
- 2개 concept dead-end 수정 (wikilink 추가)
- Obsidian vault `HermesBrain` 경로: `/Volumes/BackUp/Zettelkasten/HermesBrain`
- Obsidian 앱 실행 중, vault 인식 정상

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
- Initial structure: SCHEMA.md, index.md, log.md, concepts/, entities/, raw/- 2026-04-26: AI 초보→시니어 리서치 완료. 12개 논문 리서치 (arXiv). 핵심 발견: 속도 따라잡을 수 있으나 깊이는 경험에서만. Epistemic Debt 문제. 저장: raw/papers/ai-novice-to-senior-research.md

## [2026-04-27] meta-evolution | 스킬 업그레이드 완료: refinement-methodology v1.1, meta-evolution-pattern v1.1

### 개선 내용
- **refinement-methodology**: iteration 기록 양식에 "다음 iteration 필요?" Yes/No 필드 추가, Cron 종료 시 두 스킬 동시 업그레이드 지시 명시
- **meta-evolution-pattern**: minor version increment (.1) 기준 명시, 두 스킬 항상 함께 버전 업 규칙 추가, Cron 종료 시 절차 4단계明确规定

### 구조화된 메모리 (Meta-Evolution)
**Goal**: 초보자 Senior-level AI 협업 방법론 연구 + Research Agent 구현

**Decisions**:
1. 논문 리서치는 arXiv만 사용 (신뢰도 높음, 무료)
2. 웹 서치는 delegate_task 사용 — 600초 타임아웃 발생으로 방법 개선 필요
3. refinement + meta-evolution 두 스킬을 함께 업그레이드하는 규칙 확립

**Learned Patterns**:
- 성공: 매 연구 종료 시 스킬 업그레이드 → 방법론이 점점 정교해짐
- 성공: iteration 기록 양식의 "다음 iteration 필요?" Yes/No로 명확화
- 실패: delegate_task의 웹 서치 600초 타임아웃 — 다음에 개선 필요

**Next Improvement**:
- arxiv 서치 방법 개선:Grok 등 대안 검색 엔진, 또는 search_files로arxiv API 직접 호출
- 4개 GitHub 레포 분석 → 엔지니어링 사고 스킬 패턴 도출
