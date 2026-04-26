---
title: 세션 백업 Cron
created: 2026-04-26
updated: 2026-04-26
type: concept
tags:
- session
- memory-driven
- cron
sources:
- raw/transcripts/sessions/20260425_discord_20260425_201750_33d57b45.md
confidence: high
---

# 세션 백업 Cron

## Overview
매일 새벽 3:50 AM KST에 실행되어 Discord/Telegram 세션을 wiki에 자동 보존하는 cron job.

## 목적
Hermes Agent는 매일 4AM에 세션이 초기화(fresh context)된다. 이전 세션의 지식을 영구 저장하여 프로젝트 연속성을 유지.

## 구성
- **Cron Job ID**: `7dc837f040a1`
- **스케줄**: `50 3 * * *` (매일 03:50 KST)
- **Delivery**: `local` (Discord/Telegram posting 없음 — 파일만 저장)
- **대상**: Discord 모든 채널/쓰레드 + Telegram 모든 세션

## 워크플로우
1. `session_search()`로 recent sessions 조회 (limit=20)
2. 메시지 5개 이상인 중요 세션 선별
3. 전체 transcript를 `raw/transcripts/sessions/YYYY-MM-DD_HHMM_<platform>_<id>.md`로 저장
4. wiki layer 업데이트 (entities/concepts/queries/)
5. `index.md`, `log.md` 갱신

## 저장 위치
```
WIKI_PATH="/Volumes/BackUp/Zettelkasten/HermesBrain"
└── raw/
    └── transcripts/
        └── sessions/
            └── YYYY-MM-DD_HHMM_<platform>_<session_id>.md
```

## 미확인 이슈
- Mac이 잠자기 상태일 때 외부 디스크(BackUp 볼륨)가 스핀업 되는지 미확인
- 첫 실행이 2026-04-26 03:50 — 결과 확인 필요

## 관련 개념
- [[기억하는-개발]] — 세션 지식의 영구 저장 패턴
- [[오케스트레이션]] — cronjob의 역할 분배

## 소스
- `raw/transcripts/sessions/20260425_discord_20260425_201750_33d57b45.md`