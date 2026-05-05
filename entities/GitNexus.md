---
title: GitNexus
created: 2026-05-02
updated: 2026-05-02
type: entity
tags: [project, code-intelligence, knowledge-graph, agent-context, mcp, tree-sitter]
source_url: https://github.com/abhigyanpatwari/GitNexus
confidence: high
---

# GitNexus

**코드베이스를 지식 그래프로 변환해 AI 에이전트에게 아키텍처 인사이트를 제공하는 Code Intelligence 도구.**

"Building nervous system for agent context" — 모든 의존성, 호출 체인, 클러스터, 실행 흐름을 지식 그래프로 색인하고, MCP 도구로 AI 에이전트가 코드를 절대 놓치지 않게 한다.

## 핵심 수치

- **6,087개 노드, 11,170개 엣지, 427개 커뮤니티** (자체 코드베이스 Graphify 분석)
- **16개 언어** 지원 (TypeScript, Python, C#, Ruby, Java, C/C++, Go, Rust, Swift, PHP, Kotlin, Dart, COBOL, Vue SFC 포함)
- **12단계 Pipeline DAG**로 지식 그래프 구축
- **1,456,676 단어** 규모 코드베이스

## 아키텍처 개요

```
gitnexus/          -- CLI + MCP 서버 + HTTP API + Ingestion Pipeline + LadybugDB
gitnexus-web/      -- Vite + React 그래프 탐색기 + AI 채팅
gitnexus-shared/   -- 공유 TypeScript 타입
├── eval/          -- 벤치마크 평가 하네스
├── .claude/       -- Claude Code 스킬 + 후크
└── .github/       -- CI 워크플로우
```

**End-to-End 흐름:** `Index → Graph → Tools`

1. **Ingestion** → 12-Phase Pipeline DAG로 `KnowledgeGraph` 생성
2. **Persistence** → LadybugDB에 저장 (`.gitnexus/`)
3. **Query Layer** → MCP(stdio), HTTP, CLI 세 가지 인터페이스

## Graphify God Nodes (자체 코드의 핵심 추상화)

| 노드 | 엣지 | 의미 |
|------|------|------|
| `User` | 256 | 최다 연결 — API/DB/인증 중심 |
| `Repo` | 161 | 저장소 관리 엔티티 |
| `App` | 96 | 애플리케이션 진입점 |
| `Add()` | 88 | 그래프 노드 추가 (핵심 뮤테이션) |
| `main()` | 84 | 진입점 |
| `parse()` | 76 | 파싱 파이프라인 중심 |

## 주요 기술 스택

- **그래프 DB**: LadybugDB (자체 내장형), KuzuDB 연동
- **파싱**: Tree-sitter 네이티브 바인딩 (WASM도 지원)
- **검색**: Hybrid BM25 + Semantic Vector (RRF fusion, K=60)
- **임베딩**: Snowflake arctic-embed-xs (384D), SHA1 증분
- **MCP**: stdio 트랜스포트, 16개 도구
- **클러스터링**: Leiden 알고리즘

## 혁신 포인트

### 1. Call-Resolution DAG (6-Stage)
언어별 `LanguageProvider` 후크로 공유 코드는 언어를 모르고, 언어별 동작은 두 지점에서만 주입:
- `inferImplicitReceiver` (Ruby의 implicit-self)
- `selectDispatch` (dispatch 전략 커스터마이징)

### 2. Scope-Resolution Pipeline (RFC #909 Ring 3)
Registry-primary 방식으로 DAG를 대체. Python, C#, TypeScript가 migrated 상태. `ScopeResolver` 인터페이스 하나만 구현하면 언어 추가 완료.

### 3. Unified Capture Tags
언어별 tree-sitter 쿼리는 다른 AST 노드명을 사용하지만, 동일한 시맨틱 캡처 태그(`@definition.class`, `@call.name` 등)로 통일.

### 4. Cross-Repo Group Bridge
`@<groupName>`으로 멀티레포 검색. Contract Registry로 레포 간 경계를 넘는 impact 분석. Reciprocal Rank Fusion으로 결과 병합.

### 5. Scope Tree Cache
Parse-Phase가 `scopeTreeCache`에 Tree를 저장 → Scope-Resolution이 재파싱 없이 읽음. Worker에서는 MessageChannel 제약으로 cache miss 시 새로 파싱.

## Wiki 통합 (자체 Wiki 생성기)

`gitnexus/src/core/wiki/` — LLM을 통한 코드베이스 Wiki 자동 생성. GraphRAG 기반.

## Editor 통합

| Editor | MCP | Skills | Hooks | 지원 수준 |
|--------|-----|--------|-------|----------|
| Claude Code | ✅ | ✅ | ✅ (Pre/PostToolUse) | Full |
| Cursor | ✅ | ✅ | — | MCP+Skills |
| Codex | ✅ | ✅ | — | MCP+Skills |
| Windsurf | ✅ | — | — | MCP |
| OpenCode | ✅ | ✅ | — | MCP+Skills |

## MCP 도구 (16개)

- `query` — 하이브리드 검색 (BM25 + 벡터)
- `context` — 심볼의 360도 뷰
- `impact` — Blast radius 분석 (upstream/downstream)
- `detect_changes` — Git diff → 영향받는 심볼
- `rename` — 그래프 지원 다중 파일 rename
- `cypher` — Ad-hoc Cypher 쿼리
- `api_impact`, `route_map`, `tool_map`, `shape_check` — API/MCP 검증
- `group_*` — 크로스레포 그룹 관리

## 관련 항목

- [[knowledge-graph-code-intelligence]] — 패턴 개념
- Call Resolution DAG ⚠️ 페이지 미생성 — DAG 패턴
- Scope Resolution Pipeline ⚠️ 페이지 미생성 — Registry-primary 패러다임
- Tree-sitter Unified Capture ⚠️ 페이지 미생성 — 통합 캡처 태그
- Ladybug DB ⚠️ 페이지 미생성 — 내장 그래프 DB
- Agent Context MCP ⚠️ 페이지 미생성 — MCP 기반 AI 컨텍스트