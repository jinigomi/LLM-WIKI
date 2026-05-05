# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-05-05] lint | Wiki 전수 진단 + 자동 교정

- **수정 파일 (5개):**
  - `entities/agent-rules-books.md` — broken wikilinks de-wikilink ([[claude-code]], [[codex]], [[cursor]], [[karpathy-guidelines]])
  - `entities/harness-engineering-wikidocs.md` — URL wikilink → markdown 링크 형식
  - `index.md` — `call-resolution-dag-scope-pipeline` 추가, 잘못된 인덱스 항목 제거
- **Log 회전:** 이전 로그 → `log-archive.md` (entries 과다 → 정리)
- **잔여 경고 (작업 불필요):**
  - Orphan pages (20): entry-point 페이지 — 자연스러운 구조
  - Tag violations (49): SCHEMA taxonomy 미등록 태그群

### 주요 발견
- Broken wikilinks: 4건 → 0 (모두 수정 완료)
- Index 오류 (file-not-in-index + index-not-in-file): 2건 → 0
- Log entries: 정리 완료 (log-archive.md로 이전)
- Frontmatter: ✅ 모두 유효
- Page size: 1건 (Learn-Harness-Engineering, 216줄 — 유지)

## [2026-05-05] ingest | graphify-analysis | agent-rules-books

- **Source:** https://github.com/ciembor/agent-rules-books (MIT, v0.5)
- **Analysis:** graphify (AST 불가 — 마크다운 only) + 수동 구조 분석
- **Raw:** `raw/articles/agent-rules-books.md`
- **Entity:** `entities/agent-rules-books.md` (보강 완료)
- **Graphify-out:** `/tmp/agent-rules-books/graphify-out/GRAPH_REPORT.md`

### Graphify 분석 요약
- 총 .md 파일: 197개 (1.7MB) — 문서 위주 (AST 분석 없음)
- 커뮤니티 4개: META / BOOKS / WORKBENCH / COMPAT
- 3-tier 릴리스: full(6,290줄) → mini(758줄, 12.1%) → nano(~330줄)
- 14권 압축률: 라인 12.1%, 크기 31.4% (full→mini)
- 호환성: 78쌍 Complementary, 11쌍 Overlap, 2쌍 Conflicting

### 교차 참조
- [[spec-driven-development]] — 규칙 선언 패턴. AI 협업과 연결
- [[subagent-driven-development]] — 멀티 에이전트 구현. 규칙 기반 품질 게이트

## [2026-05-03] lint | Wiki 전수 진단 & 교정

- **초기 진단:** 29 broken wikilinks, 5 missing frontmatter, 5 tags outside taxonomy, 3 index missing, 1 source drift
- **교정 결과:** CLEAN (0 errors)

### 교정 내역
1. **Broken wikilinks (29→0):** 한글/영문 wikilink 표준화
2. **Missing frontmatter (5→0):** `tags` 필드 5개 페이지에 추가
3. **Tags taxonomy:** SCHEMA.md에 obsidian, scraping, stealth, spider, wiki-integration 등 추가
4. **Index 누락 (3→0):** `obsidian-skills`, `Scrapling`, `git-agent-loop` 등록
5. **Source drift (1→0):** Google Drive 소스 — 메모 추가