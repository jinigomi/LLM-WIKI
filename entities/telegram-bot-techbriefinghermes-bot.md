---
title: Telegram Bot @TechBriefingHermes_bot
created: 2026-04-26
updated: 2026-04-26
type: entity
tags:
- telegram
- limitation
- bot
- delivery
sources:
- raw/transcripts/sessions/20260425_discord_cron_88296c037dfa_20260425_060003.md
- raw/transcripts/sessions/20260425_discord_cron_eb1c45bdbe55_20260425_180059.md
confidence: high
contested: false
---

# Telegram Bot @TechBriefingHermes_bot

## Overview
Tech RSS 브리핑을 Telegram으로 배송하려던 봇. 현재 고장 상태.

## 현재 상태
**고장** — 2026-04-25부터 401 Unauthorized 오류 발생

## 기술 정보
- **Bot Username**: @TechBriefingHermes_bot
- **Bot Token**: `7825197564:AAFHZ5Z0dN_xN5lL3k6K8dCq9vR2wY5uT0z` (무효)
- **Chat ID**: `5502804374` (Chat not found)
- **HTTP Status**: 401 Unauthorized

## 증상
1. Python `urllib`로 직접 Telegram API 호출 → `HTTP Error 401: Unauthorized`
2. `send_message` tool → `"Chat not found"`
3. 매 시도마다 인증 실패

## 해결 필요
- Bot token 재생성 필요 (@BotFather에서 /revoke)
- Chat ID 재확인 필요
- 또는 Discord를 PRIMARY로 전환

## 관련 개념
- rss-브리핑-파이프라인 — 이 봇이 속한 전체 자동화 파이프라인
- 에러-에스컬레이션 — Telegram → Discord 폴백 패턴
- Discord 채널 #1497196158248947842 — 현재 작동하는 대안 (위키 엔티티는 제거됨)

## 소스
- `raw/transcripts/sessions/20260425_discord_cron_88296c037dfa_20260425_060003.md`
- `raw/transcripts/sessions/20260425_discord_cron_eb1c45bdbe55_20260425_180059.md`