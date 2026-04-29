---
title: "Hermes Agent 메모리 아키텍처 분석 (Manthan Gupta)"
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [hermes, memory, architecture, prompt-caching, analysis, source-analysis]
sources: [raw/articles/manthan-gupta-hermes-memory-analysis-2026-03-20.md]
confidence: high
aliases: [Hermes Memory Architecture, Hermes 메모리 구조]
---

# Hermes Agent 메모리 아키텍처 분석

Manthan Gupta가 Hermes Agent의 오픈소스 코드와 문서를 직접 분석한 심층 기술 분석. 블랙박스 역공학이 아닌 소스코드 기반 분석이라는 점에서 높은 신뢰도를 가진다.

## 개요

**핵심 주장:** Hermes에는 메모리 시스템이 하나가 아니라 **4개(5개)** 있다. ChatGPT/Claude/OpenClaw와 달리 프롬프트 안정성(prompt stability)을 최우선 제약으로 두고, 작은 핫 메모리 + 콜드 검색 레이어로 아키텍처를 구성했다.

## 5계층 메모리 아키텍처

### Layer 1: Frozen Prompt Memory (핫 메모리)
- **저장소:** `~/.hermes/memories/MEMORY.md` (~2,200자), `USER.md` (~1,375자)
- **총 ~1,300 토큰** — 의도적으로 극도로 작게 유지
- **특징:**
  - § delimiter 기반 평문 파일 (No Vector DB, No Binary Store)
  - 문자 수 제한 사용 (토큰 제한 아님 → 모델 비종속적)
  - 세션 시작 시 스냅샷 생성 후 동결 — 중간 수정은 디스크에만 기록, 새 세션/압축 시점에 반영
  - 큐레이트된 상태, **일기 아님** — 태스크 진행/결과/TODO 금지

### Layer 2: session_search (에피소딕 회상)
- **저장소:** `~/.hermes/state.db` (SQLite + FTS5 전문 검색)
- **테이블:** sessions, messages, FTS5 index, parent_session_id lineage
- **파이프라인:** 검색 → 요약 → 주입
- **사용 케이스:** "지난주에 이거 논의했나?", "X에 대해 뭘 했었지?"

### Layer 3: Compression + Memory Flush
- 세션 성장 → **memory flush** (모델이 중요한 것만 memory에 저장) → compression (중간 요약) → prompt rebuild (fresh memory snapshot)
- 요약 전에 한 번 더 모델에게 "기억할 것 저장하라"는 기회를 준다 — 손실 보호 장치

### Layer 4: Skills (절차적 메모리)
- `~/.hermes/skills/` — 재사용 가능한 워크플로우 지식
- **토큰 효율성:** 압축된 skills index만 프롬프트에 주입, 필요 시 전체 skill 로드
- 대부분의 메모리 시스템이 의미 기억(semantic)에만 집중하는 것과 대조적

### Layer 5: Honcho (고차원 사용자 모델링, 선택적)
- 크로스 세션 사용자 모델링, 크로스 머신/플랫폼 연속성
- **캐시 인식 통합:** 첫 턴에서만 시스템 프롬프트에 포함, 이후 턴에서는 API-call time에만 부착
- 사용자와 AI 어시스턴트 **양쪽**을 모델링

## 프롬프트 어셈블리 구조

```
persona → memory → skills index → conversation → tool schemas
```

핵심 설계 원칙: **stable prefix는 가능한 한 오래 유지** (provider-side prompt 캐싱 최적화)

## OpenClaw와의 비교

| 차원 | Hermes | OpenClaw |
|------|--------|----------|
| 프롬프트 메모리 | 극단적 제한 (1,300 tokens) | Markdown-first, 더 큰 용량 |
| 세션 히스토리 | SQLite + FTS5 | Daily logs + long-term memory files |
| 과거 회상 | session_search (on-demand) | Hybrid search over stored notes |
| 절차적 메모리 | Skills 시스템 | 없음 |
| 사용자 모델링 | Honcho (선택적, cache-aware) | 없음 |
| 캐시 인식 | 최우선 설계 제약 | 덜 중요시 |

## 핵심 인사이트

1. **핫 / 콜드 메모리 분리** — 항상 필요한 것만 프롬프트에, 나머지는 검색으로
2. **프롬프트 안정성을 1급 제약으로** — frozen snapshot, delayed update, turn-level injection
3. **메모리는 복수형** — 의미 기억 + 에피소드 회상 + 절차 기억 + 고차원 사용자 모델
4. **"더 많이 기억하기"가 아니라 "적절한 레이어에, 적절한 비용으로"**

## 연결 개념

- [[anthropic-claude-think-tool]] — Claude도 도구 사용 시점에 think tool로 컨텍스트 보존
- [[hermes-vs-boop]] — Boop Agent와의 메모리 아키텍처 비교
- [[context-rot]] — 컨텍스트 손실에 대응하는 또 다른 접근
- [[harness-engineering]] — 메모리/캐시 설계도 Harness Engineering의 한 축