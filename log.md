# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-05-07] lint | 자동 정리

### index.md 수정 (2건)
- 헤더: Total pages 69 → 72 (정정)
- Comparisons (3) 섹션 신규 추가: don-cheli-vs-cc-sdd, hermes-vs-boop, openspec-vs-superpowers

### 발견 사항 (삭제 보류 — 정제된 콘텐츠 보유)
- 총 .md 파일: 88개. 스텁 파일 0개, graphify 부실 아티팩트 0개.
- `raw/articles/agentic-ai-engineer-roadmap-2026-qa.md` (41KB): entity (4.9KB)에 요약 존재하지만 원본 보존
- `raw/articles/agent-rules-books.md` (2KB): entity (6.9KB)는 분석본, raw는 원본 테이블 — 다른 콘텐츠 타입
- `entities/harness-engineering-wikidocs.md`: "미완성" 키워드 포함 → 분석 섹션 내 메타 코멘트, 정상 콘텐츠
- `queries/vibe-coding-beginner-checklist.md`: 인덱스 미등재이나 유효한 체크리스트 → 유지

### 최종 상태
- 총 .md 파일: 88개 (변경 없음)
- 디렉토리별: concepts/23, entities/41, comparisons/3, queries/1, docs/research-reports/3, raw/articles/14, root/3
- 인덱스 등재: 72개 (Comparisons 3개 추가)

## [2026-05-06] lint | 자동 정리

### 삭제 (2개 파일, 5,376b)
- `log-archive.md` (212b) — 빈 스텁. 실제 로그 내용 없음.
- `raw/articles/long-running-agents-addy-osmani-2026.md` (5,164b) — HTML 스크래핑 잔여물. `entities/long-running-agents.md`가 이미 정리된 요약 보유.

### index.md 수정
- `raw/articles/` 수 11 → 12로 수정
- 존재하지 않는 `raw/articles/harness-engineering/` 항목 제거
- 존재하지 않는 `notebooklm-note-wiki-vibe-coding-beginner-methodology-report` 항목 제거
- 존재하지 않는 `/Users/bricoleur/artifacts/...` 경로 제거 (외부 경로)

### 발견 사항 (삭제 보류)
- `raw/articles/agentic-ai-engineer-roadmap-2026-qa.md` (41KB): entity (4.9KB)에 요약 존재하지만 원본이 큼 → 유지 (원본/raw 원칙)
- `raw/articles/agent-rules-books.md` (2KB): entity (6.9KB)에 분석+요약 존재 → 유지 (다른 콘텐츠 타입)
- `raw/articles/deep-learning-with-python-3e-toc.md`: TOC vs 분석으로 다른 콘텐츠 → 유지
- 깨진 wikilinks: 없음 ✅
- 고아 페이지: 없음 ✅
- graphify 불완전 아티팩트: 없음 ✅ (이전 탐지 오류로 판명)

### 최종 상태
- 총 .md 파일: 89 → 87개
- 디렉토리별: concepts/24, entities/41, comparisons/3, queries/1, docs/2, raw/13, root/3

## [2026-05-06] research | 초보자 Senior-level AI 협업 방법론

- **arXiv 재시도**: 5개 쿼리 → API 복구 ✅, 관련 논문 없음 (기존 47편 유지)
- **Wiki lint**: 자동 정리 완료 (2파일 삭제, 깨진 wikilinks 0건)
- **보고서**: `docs/research-reports/2026-05-06-research-progress.md` 저장
- **스킬**: 변경 없음 (v1.5.0 / v1.6.0 유지) — 새로운 방법론적 패턴 미발견
- **다음**: Semantic Scholar 확대 검색, Framework v2.0 개발 착수

## [2026-05-05] research | 초보자 Senior-level AI 협업 방법론

- **arXiv 일시 불가**: 19개 쿼리 → 전부 429/timeout. 47편 (2026-05-02) 유지
- **보고서**: `docs/research-reports/2026-05-05-research-progress.md` 저장
- **스킬**: 변경 없음 (v1.5.0 / v1.6.0 유지) — 새로운 방법론적 패턴 미발견
- **다음**: arXiv 재검색 (2026-05-12), NotebookLM pipeline 복구, Framework v2.0

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

## [2026-05-05] ingest | deep-learning-with-python-3e

- **소스:** https://deeplearningwithpython.io/chapters/ (Manning MEAP)
- **분석:** TOC 페이지 (200 OK) + Manning.com 상세 정보 + GitHub 노트북 목록
- **참고:** 개별 챕터 페이지는 403 Forbidden — 본문 직접 수집 불가
- **Raw:** `raw/articles/deep-learning-with-python-3e-toc.md`
- **Entity:** `entities/deep-learning-with-python-3e.md`

### 서적 정보
- **저자:** François Chollet (Keras 창시자) + Matthew Watson (Google Keras 메인테이너)
- **출판:** Manning, ISBN 9781633436589, MEAP
- **GitHub:** fchollet/deep-learning-with-python-notebooks (★20K)
- **20챕터 커버:** Keras 3 + PyTorch/JAX/TensorFlow 4백엔드 + 생성형 AI + LLM

### 교차 참조
- [[hands-on-large-language-models]] — LLM 시각화 교과서와 보충 관계
- [[knowledge-graph-code-intelligence]] — Keras 코드 인텔리전스 연결

- **index.md:** Entities 40 → 41, Total pages 68 → 69

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