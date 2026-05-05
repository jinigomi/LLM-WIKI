---
title: Knowledge Graph as Code Intelligence
created: 2026-05-02
updated: 2026-05-02
type: concept
tags: [code-intelligence, knowledge-graph, agent-context, static-analysis, llm-tooling]
confidence: high
source: GitNexus 아키텍처 분석
---

# Knowledge Graph as Code Intelligence

**코드베이스를 지식 그래프로 변환해 AI 에이전트에게 구조적 인사이트를 제공하는 패러다임.**

단순 텍스트 검색(RAG)이 아닌, 심볼·호출·상속·임포트 관계를 그래프로 모델링하여 AI가 코드의 전체적 아키텍처를 이해하게 한다.

## 문제: AI 에이전트가 코드를 놓치는 이유

- **Grep은 텍스트만 찾는다** — `User`를 검색하면 500개 파일, 하지만 `deleteUser`가 어디서 호출되는지는 모른다
- **RAG는 의미만 본다** — 비슷한 의미의 코드는 찾지만, 호출 체인의 upstream impact는 추적하지 못한다
- **정적 분석은 AST 수준에서 멈춘다** — 어떤 함수가 존재하는지만 알려주고, 실행 흐름·클러스터·blast radius는 모른다

## 해결: 코드를 그래프로

지식 그래프는 다음을 모델링한다:

```
심볼 ──CALLS──▶ 심볼     (호출 관계)
심볼 ──IMPLEMENTS──▶ 인터페이스  (구현 관계)
심볼 ──EXTENDS──▶ 클래스 (상속 관계)
심볼 ──IMPORTS──▶ 모듈   (의존 관계)
심볼 ──MEMBER_OF──▶ 커뮤니티 (Leiden 클러스터링)
심볼 ──STEP_IN_PROCESS──▶ 프로세스 (실행 흐름)
```

### 핵심 질문들

| 질문 | 그래프 질의 | 기존 방식 |
|------|-----------|---------|
| "이 함수를 바꾸면 뭐가 깨지나?" | `impact(upstream)` | 수동 grep |
| "이 PR에서 어떤 심볼이 바뀌었지?" | `detect_changes(scope: staged)` | `git diff` 읽기 |
| "이 API 엔드포인트는 어디서 호출하지?" | `route_map` | IDE find references |
| "이 심볼의 전체 context는?" | `context` (callers + callees + processes) | 파일 하나씩 열기 |
| "코드베이스의 주요 클러스터는?" | Leiden communities | 직관/경험 |

## AI 에이전트 통합 (MCP)

MCP(Model Context Protocol)를 통해 AI 에이전트가 그래프를 도구처럼 쿼리:

```
Agent: "deleteUser를 수정하려고 하는데, 영향 범위를 알려줘"
       → gitnexus_impact(target="deleteUser", direction="upstream")
       → "HIGH risk: 47 downstream consumers, 3 API routes, 12 tests"
```

이 패턴은 Gemini CLI, Claude Code, Cursor, Codex, OpenCode 등에서 동작.

## 아키텍처 패턴

### 1. Pipeline DAG
12단계 방향성 비순환 그래프로 점진적 변환:
```
scan → structure → [markdown, cobol] → parse → [routes, tools, orm] 
  → crossFile → mro → communities → processes
```

### 2. Single Graph Accumulator
모든 단계가 동일한 `KnowledgeGraph`를 변이 — 최종 출력 = 그래프

### 3. Unified Capture Tags
Tree-sitter 언어별 쿼리가 다른 AST 노드명을 쓰지만, 동일한 시맨틱 캡처 태그(`@definition.class`, `@call.name`)로 통일 → 다운스트림은 언어를 모른다

### 4. Language Provider Hooks
공유 코드는 언어를 모르고, 언어별 동작은 후크 지점에서만 주입:
- Import resolution: `named` / `wildcard` / `namespace`
- Call dispatch: `inferImplicitReceiver` / `selectDispatch`
- MRO strategy: `first-wins` / `c3` / `ruby-mixin`

## GitNexus 적용 결과

자체 코드베이스 Graphify 분석:
- 6,087개 노드, 11,170개 엣지, 427개 커뮤니티
- God Nodes: `User`(256), `Repo`(161), `parse()`(76)
- 75% EXTRACTED, 25% INFERRED (avg confidence 0.79)

## 관련 항목

- [[GitNexus]] — 구체적 구현체
- Call Resolution DAG ⚠️ 페이지 미생성 — 호출 해석을 위한 6-Stage DAG
- Scope Resolution Pipeline ⚠️ 페이지 미생성 — Registry-primary 해석 파이프라인
- Tree-sitter Unified Capture ⚠️ 페이지 미생성 — 통합 캡처 태그 패턴
- Agent Context MCP ⚠️ 페이지 미생성 — MCP로 AI에게 컨텍스트 제공