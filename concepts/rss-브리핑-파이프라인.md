---
title: RSS 브리핑 파이프라인
created: 2026-04-26
updated: 2026-04-26
type: concept
tags: [오케스트레이션, cron, telegram, discord]
sources: [raw/transcripts/sessions/20260425_discord_cron_88296c037dfa_20260425_060003.md, raw/transcripts/sessions/20260425_discord_cron_eb1c45bdbe55_20260425_180059.md]
confidence: high
---

# RSS 브리핑 파이프라인

## Overview
매일 아침/저녁 RSS 피드를 수집하여 Discord/Telegram으로 Tech 뉴스를 자동 배송하는 시스템. Hermes Agent의cronjob으로 구동된다.

## 구성 요소

| компонент | 역할 |
|-----------|------|
| `rss-briefing` skill | RSS 수집 → 번역/요약 → 포맷 |
| cron job | 매일 06:00, 18:00 자동 실행 |
| Telegram Bot | @TechBriefingHermes_bot (현재 고장) |
| Discord Channel | 1497196158248947842 (#knowledge-scaner) |

## 현재 상태 (2026-04-26)
- **Telegram**: 고장 — bot token invalid (401 Unauthorized), chat ID not found
- **Discord**: 정상 작동 — channel 1497196158248947842에 자동 배송
- **배송률**: 21-23개 기사 중 13개만 최종 리포트에 포함 (길이 제한)

## 문제점
Telegram bot (`@TechBriefingHermes_bot`, token: `7825197564:AAFHZ5Z0dN_xN5lL3k6K8dCq9vR2wY5uT0z`)이 401 오류를 반환. 재설정 또는 재생성 필요.

## 관련 개념
- [[오케스트레이션]] — cronjob 패턴의 일종
- [[에러-에스컬레이션]] — Telegram 고장 시 Discord로 폴백

## 소스
- `raw/transcripts/sessions/20260425_discord_cron_88296c037dfa_20260425_060003.md`
- `raw/transcripts/sessions/20260425_discord_cron_eb1c45bdbe55_20260425_180059.md`