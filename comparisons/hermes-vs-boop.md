---
title: Hermes vs Boop Agent 비교
created: 2026-04-28
updated: 2026-04-28
type: comparison
tags: [comparison, agent, multi-agent, orchestration, hermes]
sources: [https://github.com/raroque/boop-agent]
confidence: high
---

# Hermes vs Boop Agent 비교

## Overview

| 항목 | Hermes Agent | Boop Agent |
|------|-------------|------------|
| **포지션** | 개발자용 AI 동료 | 비개발자용 AI 비서 |
| **인터페이스** | Discord / Telegram / CLI / SMS | iMessage only |
| **아키텍처** | 단일 Agent (도구 풀 접근) | Dispatcher + Worker 분리 |
| **Sub-agent** | `delegate_task` (선택적) | **모든** 실작업이 sub-agent |
| **실행 환경** | 로컬 머신 직접 (터미널/파일) | Claude Agent SDK (격리) |
| **통합** | MCP 직접 구성 | Composio OAuth 중개 (23개) |
| **Memory** | Markdown DB + keyword/vector | Convex + vector + segmentation + decay |
| **Memory 관리** | 수동 (`memory` tool) | 자동 (consolidation: proposer→adversary→judge) |
| **안전장치** | 없음 (직접 실행) | Drafts confirm flow |
| **Cron** | `cronjob` tool (프롬프트 기반) | `croner` (코드 기반) + notify 빌트인 |
| **Skill 관리** | `skill_manage` (create/patch/view) | `.claude/skills/` (수동, CLI 없음) |
| **전문 도구** | 브라우저 자동화, ML inference, 논문 검색, MCP | 없음 (Composio 위주) |
| **스택** | Python | TypeScript + Convex |
| **배포** | 로컬/서버 | 로컬 + Convex 클라우드 |
| **라이선스** | — | MIT |

## 아키텍처 차이

### Hermes: 단일 Agent + 선택적 위임
```
사용자 → Hermes → tools (전체 접근)
                   └ delegate_task (필요시만)
```
- 모든 도구에 접근 가능
- 직접 실행이 기본, 복잡할 때만 sub-agent spawn
- 컨텍스트 윈도우 하나에 모든 상태가 쌓임

### Boop: Dispatcher-Worker 강제 분리
```
사용자 → Interaction Agent → spawn → Execution Agent(s) → draft → Interaction Agent → 사용자
         (WebSearch 등 금지)            (실행만 전담)
```
- Dispatcher는 절대 팩트를 직접 말하지 못함
- Execution Agent는 절대 유저와 직접 소통하지 못함
- 각 역할에 엄격한 tool 제약

## Memory 관리 차이

### Hermes
- 수동 관리: `memory(action='add')`, `replace`, `remove`
- 구조: memory/user 두 개 타겟, char 제한 있음
- decay/정리: 유저가 수동으로 관리하거나 오래된 항목 덮어쓰기

### Boop
- 자동 관리: segment(identity/correction/preference/...) → decay rate 차등
- Supersede chain: 새 정보가 오래된 정보를 자동 대체
- Consolidation (24시간): Proposer 제안 → Adversary 반박 → Judge 판결
- Correction 불멸: 유저가 "아니다" 하면 decay rate 거의 0

## 각자의 강점

### Hermes가 더 나은 점
- **로컬 개발 워크플로우** — 파일시스템, 터미널, 코드 실행 직접 접근
- **멀티플랫폼** — Discord, Telegram, CLI, SMS 중 선택
- **Skill 생태계** — create/patch/view 도구로 스킬 관리, 40+ 내장 스킬
- **연구/ML 도구** — 논문 검색, 브라우저 자동화, oMLX 추론

### Boop이 더 나은 점
- **비개발자 UX** — iMessage로 "그냥 문자 보내면" 됨
- **통합 범위** — Composio로 23개 SaaS 연동 (OAuth, API key 불필요)
- **Memory 자동화** — decay + consolidation으로 수동 관리 불필요
- **안전장치** — Drafts confirm으로 민감 작업 실수 방지

## Hermes가 도입할 만한 패턴

1. **Consolidation (적대적 검증 기반 memory decay)**
   - 문제: Hermes memory가 쌓이면 수동으로 `supersedes` 써야 함
   - 해결: Proposer→Adversary→Judge 파이프라인으로 자동 정리

2. **Drafts confirm flow**
   - 문제: Hermes는 모든 도구를 직접 실행 — 메시지 전송, 캘린더 생성 등 민감 작업에 안전장치 없음
   - 해결: 특정 도구는 draft → user confirm → execute

3. **Ack 최적화**
   - 문제: 긴 작업(30초+) 시 유저가 기다리는 동안 아무 피드백 없음
   - 해결: `delegate_task` 호출 직전 한 줄 acknowledgment 먼저 전송

## 관련 페이지
- [[boop-agent]] — Boop Agent 상세 분석
- [[agent-memory-systems]] — 에이전트 메모리 설계 패턴
- [[subagent-driven-dev-pattern]] — Subagent-Driven Development 패턴