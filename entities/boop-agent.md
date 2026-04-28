---
title: Boop Agent
created: 2026-04-28
updated: 2026-04-28
type: entity
tags: [agent, multi-agent, open-source, orchestration, comparison]
sources: [https://github.com/raroque/boop-agent]
confidence: high
---

# Boop Agent

## Overview

"text-an-agent" 컨셉의 오픈소스 개인 비서. iMessage로 자연어 요청을 보내면 백그라운드에서 Claude Agent SDK 기반 execution sub-agent들이 실작업을 수행하고 결과를 돌려줌. **비개발자 대상 AI 비서** 포지션.

- **저장소:** https://github.com/raroque/boop-agent
- **라이선스:** MIT
- **스택:** TypeScript + Express + WebSocket + Convex (벡터 DB) + Composio (통합) + Claude Agent SDK

## Architecture

```
iMessage → Express 서버 (port 3456)
                │
        ┌───────┴───────┐
        ▼               ▼
  Interaction Agent  Execution Agent
   (Dispatcher)       (Worker x N)
   claude-sonnet-4-6  claude-sonnet-4-6
        │               │
        │    spawn      │
        │──────────────▶│  ┌─────────────┐
        │               │  │ Composio    │
        │               │──│ (23개 toolkit)│  ← MCP
        │               │  └─────────────┘
        │               │  ┌─────────────┐
        │               │  │ Drafts      │
        │               │──│ (안전장치)    │
        │               │  └─────────────┘
        │               │  ┌─────────────┐
        │               │  │ .claude/    │
        │               │──│ skills/     │  ← 파일시스템 스킬
        │               │  └─────────────┘
        │
        ▼
   Memory (Convex DB + Vector)
   Automations (Cron → Execution Agent)
   Consolidation (Proposer↔Adversary↔Judge 3-stage)
```

## Core Design Decisions

### 1. Dispatcher-Worker 분리 (핵심 설계)

| 계층 | 역할 | 도구 제약 |
|------|------|-----------|
| **Interaction Agent** | 유저 이해, spawn 결정, 결과 relay | `WebSearch`, `WebFetch`, `Bash`, `Read`, `Write`, `Edit` **금지** |
| **Execution Agent** | 실작업 수행 (검색/통합/파일) | `WebSearch`, `WebFetch`, Skill, Composio tools만 허용 |

Dispatcher는 **절대 팩트를 직접 말하지 못하고** 무조건 sub-agent를 spawn해야 함. "Even if you're 99% sure" — spawn rule.

### 2. Ack 패턴 (iMessage UX 최적화)
`send_ack → spawn_agent → (wait) → final reply`

유저가 기다리는 10~30초 동안 "On it — one sec 🔍" 같은 한 줄을 먼저 전송.

### 3. Memory 시스템 (계층형 Decay)

```
Segment: identity > correction > relationship > preference > project > knowledge > context
Tier:   permanent    long         long          long        long      long        short
Decay:  0.01         0.015        0.02          0.02        0.025     0.03        0.08
```

- **Vector + Substring dual search** (embedding → fallback substring)
- **Supersede chain** — 새 정보가 오래된 정보를 대체
- **Correction은 거의 불멸** — 유저 피드백 최우선

### 4. Consolidation (Proposer → Adversary → Judge)
24시간마다 3단계 memory decay 관리:
1. **Proposer** (sonnet) → merge/supersede/prune 제안
2. **Adversary** (haiku) → 각 제안에 이의제기 (low/medium/high)
3. **Judge** (sonnet) → 승인/거부 판결

적대적 검증(adversarial validation)으로 memory decay 자동화.

### 5. Drafts 안전장치
Execution agent는 절대 직접 send하지 않음. 민감한 작업(메시지 전송, 결제, 캘린더 생성)은 `save_draft` → 유저 확인 → `send_draft` 패턴.

### 6. Composio 통합 (23개 toolkit)
OAuth 중개로 유저가 API key 없이 사용:
Gmail, Google Calendar, Drive, Sheets, Docs, Slack, GitHub, Linear, Notion, HubSpot, Discord, Trello, Asana, Jira, Airtable, Figma, Dropbox, Stripe, Supabase, Granola, Salesforce, Twitter/X, LinkedIn

### 7. Automations (Cron → Execution Agent)
cron 표현식으로 등록 → 주기적 실행 → 결과를 iMessage로 push. **notify 빌트인**.

## Hermes와 비교

상세 비교: [[hermes-vs-boop]]

주요 차이점:
- **대상:** Boop = 비개발자, Hermes = 개발자
- **인터페이스:** Boop = iMessage only, Hermes = Discord/Telegram/CLI
- **Sub-agent:** Boop = 모든 실작업이 sub-agent, Hermes = delegate_task (선택적)
- **통합:** Boop = Composio (OAuth), Hermes = MCP 직접 구성
- **Memory 관리:** Boop = 자동 (consolidation), Hermes = 수동 (`memory` tool)
- **안전장치:** Boop = Drafts confirm, Hermes = 없음

## 배울 점

Hermes가 차용할 만한 패턴:
1. **Consolidation** — 적대적 검증 기반 memory decay 자동화
2. **Drafts confirm flow** — 민감 작업 안전장치
3. **Ack 최적화** — 긴 작업 전 한 줄 먼저 던지기