---
title: "Hermes Agent 15가지 숨은 기능"
type: concept
status: evergreen
created: 2026-04-29
updated: 2026-04-29
source: "raw/articles/sharbel-15-hermes-features-2026-04-29.md"
source_url: "https://x.com/sharbel/status/2049158152709382177"
author: Sharbel (@sharbel)
tags:
  - hermes-agent
  - slash-commands
  - skills
  - agent-workflow
  - productivity
references:
  - "anthropic-building-effective-agents.md"
  - "context-rot.md"
---

# Hermes Agent 15가지 숨은 기능

> Sharbel(@sharbel)이 정리한 Hermes Agent의 잘 알려지지 않은 15가지 기능. 대부분의 사용자가 Hermes를 "더 똑똑한 ChatGPT" 수준으로만 사용하고 있다는 문제의식에서 출발.

## 핵심 인사이트

**"대부분의 사용자는 Hermes 기능의 8%만 사용 중이다."**

Hermes는 Telegram 봇이 아니라 persistent memory, 100+ pre-built skills, filesystem rollback, session branching, mid-flight steering, 17-platform messaging, voice mode, multi-provider routing, auxiliary model routing, cron automation, webhook integration, custom slash command를 갖춘 **완전한 에이전트 플랫폼**이다.

## 15가지 기능 분류

### 초기 설정 (1-4): 대부분 건너뛰는 것들

| # | 기능 | 설명 |
|---|------|------|
| 1 | `/personality` + `SOUL.md` | 에이전트의 목소리/성격을 영구 정의. 세션·플랫폼·백엔드 관계없이 적용 |
| 2 | `MEMORY.md` + `USER.md` | 프로젝트 지식(MEMORY)과 사용자 정보(USER)를 FTS5+LLM으로 인덱싱. 8주 전 기억도 끌어옴 |
| 3 | `/insights [days]` | 전체 세션 분석: 토큰 사용량, 비용, 병목, 반복 작업 |
| 4 | `/snapshot` | Hermes 설정/상태 전체 스냅샷. 실험 후 복원 가능 |

### 중간 제어 (5-9): 아무도 안 쓰는 것들

| # | 기능 | 설명 |
|---|------|------|
| 5 | `/branch` (`/fork`) | 세션 분기 — git처럼 다른 경로 탐색 후 원래 세션 유지 |
| 6 | `/rollback` | 파일시스템 체크포인트. 에이전트가 코드를 망가뜨려도 복원 |
| 7 | `/btw` | 컨텍스트는 유지하되 툴 호출 없이, 기록되지 않는 일회성 질문 |
| 8 | `/steer` + `/queue` | 실행 중인 작업에 실시간 지시. `/queue`로 다음 턴 예약 |
| 9 | `/yolo`, `/fast`, `/reasoning` | 승인 건너뛰기, 저지연 모드, 추론 레벨 설정 |

### 모델 자유도 (10-11): 벤더 종속 없는 설계

| # | 기능 | 설명 |
|---|------|------|
| 10 | `/model [--provider]` | Opus 4.7, GPT-5.5, OpenRouter, Gemini, Kimi 등 실시간 전환 |
| 11 | Auxiliary models | 컨텍스트 압축·세션 요약·타이틀 생성 등 부가 작업에 별도 모델 할당 |

### 도달 범위 (12-14): 아무도 활성화 안 하는 것들

| # | 기능 | 설명 |
|---|------|------|
| 12 | 17-platform gateway | Telegram, Discord, Slack, WhatsApp, Signal, Email, SMS, Matrix 등 |
| 13 | `/voice` | CLI, Telegram, Discord에서 실시간 음성 |
| 14 | Cron + `/webhook-subscriptions` | 자연어 스케줄링 + 외부 서비스 웹훅 (제로 토큰 비용) |

### 진짜 사용자를 가르는 것 (15)

| # | 기능 | 설명 |
|---|------|------|
| 15 | **Skills as slash commands** | 100+ 내장 스킬을 `/`로 호출. 커스텀 스킬 작성 가능 (`/sage` 예시) |

## Sharbel의 결론

> "당신은 persistent memory, 100+ pre-built skills, filesystem rollback, session branching, mid-flight steering, 17-platform messaging reach, voice mode, native multi-provider routing, auxiliary model routing, cron automation, webhook integration, custom slash command를 가진 에이전트에 비용을 지불했다. 그리고 그것을 '조금 더 나은 Telegram 봇'으로 사용해왔다."

## 연결된 개념

- [[harness-engineering]] — AI 에이전트 행동 통제와 예측 가능성
- `anthropic-building-effective-agents.md` — 효과적인 에이전트 설계 원칙
- [[오케스트레이션]] — 에이전트 간 작업 조율 패턴
