---
title: Stash
created: "2026-05-02"
aliases: [Stash]
type: entity
added: 2026-05-01
source: https://github.com/alash3al/stash
tags: [mcp, memory, llm, agent, postgres, pgvector, consolidation]
---

# Stash

**AI Agent용 영구 기억 시스템 (Persistent Memory)**

> "Your AI has amnesia. We fixed it."

**저자:** [Mohamed Al Ashaal (@alash3al)](https://github.com/alash3al)
**라이선스:** Apache 2.0
**언어:** Go
**버전:** v0.2.8 (2026-05-01 기준)
**문서:** https://alash3al.github.io/stash/

## 핵심 컨셉

Stash는 LLM 에이전트에게 **영구 기억(Persistent Memory)**을 부여하는 오픈소스 시스템이다. 에이전트가 세션을 넘나들며 사실을 기억하고, 관계를 추출하고, 패턴을 발견하며, 자기 지식을 축적할 수 있게 한다.

핵심 단계: **Episodes → Facts → Relationships → Patterns → Wisdom**

## 아키텍처

```
┌──────────────────────────────────────────────┐
│             MCP Protocol (stdio)              │
│  init · remember · recall · forget · context  │
│  consolidate · query_facts · list_hypotheses  │
│  trace_causal_chain · resolve_contradiction   │
│  create_goal · list_goals · create_failure ...│
├──────────────────────────────────────────────┤
│              Brain (core)                     │
│  Consolidation Pipeline (8 stages)             │
│  Namespace isolation · Vector recall           │
├──────────────────────────────────────────────┤
│  Reasoner  │  Embedder  │  Config  │  Queries │
├──────────────────────────────────────────────┤
│         PostgreSQL + pgvector                 │
└──────────────────────────────────────────────┘
```

### 주요 구성요소

| 계층 | 설명 |
|------|------|
| **MCP Server** | 22개 MCP 도구 제공 — remember, recall, consolidate, hypothesis, causal, goals, failures 등 |
| **Brain** | 핵심 로직 — 모든 CRUD 연산 + Consolidation 파이프라인 오케스트레이션 |
| **Reasoner** | LLM 호출 인터페이스 (기본: OpenAI). 구조화된 Fact/Relationship/Pattern 추출 |
| **Embedder** | OpenAI 임베딩 + LRU 캐시. 벡터 임베딩 생성 및 캐싱 |
| **Queries** | PostgreSQL 쿼리 생성기. 동적 SQL 빌더 |
| **Config** | BatchSize, SimilarityThreshold(0.85), DedupThreshold(0.95), DecayFactor(0.95) 등 |

## 8단계 Consolidation 파이프라인

매 `consolidate` 호출마다 신규 데이터만 증분 처리한다:

| Stage | 설명 | 입력 → 출력 |
|-------|------|-------------|
| **1** | Episodes → Facts | 유사도 기반 클러스터링 → LLM 구조화 → Fact 생성 |
| **2** | Facts → Relationships | 각 Fact에서 entity → relation → entity 추출 |
| **3** | Facts → Causal Links | Fact 쌍에서 인과관계(cause→effect) 추출 |
| **4** | Contradiction Detection | 동일 entity+property에 대한 상반된 사실 감지 및 자동 해결 |
| **5** | Confidence Decay | 시간 경과에 따른 Fact 신뢰도 감소 + 만료 처리 |
| **6** | Goal Progress | 새 Fact가 기존 Goal에 progress/complete/contradict 시그널인지 평가 |
| **7** | Failure Patterns | 과거 실패 반복 감지 + 상위 패턴 추출 |
| **8** | Hypothesis Evidence | 새 증거가 가설을 support/weaken/contradict 하는지 스캔 → 자동 확정/기각 |

### Consolidation 결과 예시
```json
{
  "namespace": "/self",
  "facts_created": 12,
  "facts_deduplicated": 3,
  "relationships_found": 8,
  "causal_links_found": 2,
  "contradictions_found": 1,
  "contradictions_auto_resolved": 1,
  "hypotheses_auto_confirmed": 1,
  "facts_decayed": 45,
  "llm_calls": 5,
  "duration": "2.3s"
}
```

## 데이터 모델

| 모델 | 설명 |
|------|------|
| **Namespace** | 메모리 격리 단위 — 트리 구조 `/self/capabilities` 같은 위계 지원 |
| **Episode** | 불변의 원시 관측 — append-only |
| **Fact** | Episode에서 추출된 구조화 지식 — entity, property, value, confidence, valid_from/until |
| **Relationship** | 엔티티 간 관계 (from_entity → relation_type → to_entity) |
| **Pattern** | Fact+Relationship에서 추상화된 패턴 |
| **CausalLink** | 인과 관계 (cause_fact → effect_fact) |
| **Hypothesis** | 검증 대기 중인 가설 — verification_plan, auto_confirm/reject 임계값 |
| **Contradiction** | 동일 entity+property에 대한 상충 정보 — auto_resolve 가능 |
| **Goal** | 세션 간 지속되는 목표 — 상태 추적, 부모-자식 위계 |
| **Failure** | 실패 기록 — 원인, 교훈, goal_id 연결 |
| **Context** | 네임스페이스당 활성 작업 상태 (focus + TTL) |
| **EmbeddingCache** | 텍스트 해시 + 모델별 임베딩 캐시 (LRU) |

## MCP 도구 목록 (22종)

| 카테고리 | 도구 |
|----------|------|
| **기본 CRUD** | `init`, `remember`, `recall`, `forget` |
| **Consolidation** | `consolidate` |
| **Namespace** | `create_namespace`, `list_namespaces` |
| **Context** | `set_context`, `get_context`, `clear_context` |
| **Fact/Rel** | `query_facts`, `query_relationships` |
| **Causal** | `create_causal_link`, `list_causal_links`, `trace_causal_chain` |
| **Hypothesis** | `create_hypothesis`, `list_hypotheses` |
| **Contradiction** | `list_contradictions`, `resolve_contradiction` |
| **Goal** | `create_goal`, `list_goals`, `mark_goal_complete` |
| **Failure** | `create_failure`, `list_failures` |

## 설치 및 실행

```bash
git clone https://github.com/alash3al/stash.git
cd stash
cp .env.example .env   # OPENAI_API_KEY, MODEL 설정
docker compose up       # Postgres + pgvector + Stash 자동 실행
```

Docker 없이도 가능:
```bash
go build -o stash ./cmd/cli
./stash serve --db-url "postgres://user:pass@localhost:5432/stash?sslmode=disable"
```

## 호환 에이전트

Claude Desktop, Cursor, Windsurf, Cline, Continue, OpenAI Agents, Ollama, OpenRouter — MCP 호환 모든 에이전트

## Graphify 분석 하이라이트

- **273 nodes · 590 edges · 24 communities**
- **God Node:** `Brain` (72 edges) — 모든 것을 연결하는 중앙 허브
- **핵심 클러스터:** CLI commands (Community 6), Data models (Community 4), Consolidation (Community 3), Reasoning (Community 8)
- **INFERRED 엣지 비율:** 47% — 코드 간 직접 호출 외 LLM 추론된 관계 다수
- **격리 노드:** 39개 — 문서화 갭 가능성

## Hermes Agent와의 비교

| 측면 | Stash | Hermes Agent |
|------|-------|--------------|
| **메모리 모델** | Episode → Fact → Pattern (8-stage) | Session → Memory → User (3-layer) |
| **인터페이스** | MCP protocol only | MCP + CLI + Discord + Telegram + REST |
| **저장소** | PostgreSQL + pgvector | SQLite + FTS5 |
| **Consolidation** | LLM 기반 자동 추론 | 명시적 `memory` 툴 호출 |
| **가설 검증** | Hypothesis + auto_confirm/reject | 없음 |
| **인과 추적** | CausalLink + trace_causal_chain | 없음 |
| **목표 추적** | Goal + progress assessment | 없음 |
| **실패 학습** | Failure + repeat detection + patterns | 없음 |

**요약:** Stash는 "스스로 생각하는 기억 시스템" — 에이전트가 단순히 저장하는 것이 아니라, LLM을 통해 지식을 추론하고 구조화한다. Hermes보다 훨씬 정교한 인지 파이프라인을 제공하지만, MCP 프로토콜만 지원하므로 통합 범위는 제한적이다.

## 참고 링크

- [alash3al.github.io/stash](https://alash3al.github.io/stash/) — 공식 문서
- [GitHub](https://github.com/alash3al/stash)