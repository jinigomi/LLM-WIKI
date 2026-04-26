# Wiki Log

> Chronological record. Append-only.

## [2026-04-25] create | Wiki initialized
- Domain: AI 협업 스킬 — 기억하는 개발 패턴
- Structure: SCHEMA.md, index.md, log.md, raw/, concepts/, entities/, comparisons/, queries/
- Source: wikidocs.net/book/19307 (12 chapters scraped via Playwright)

## [2026-04-25] ingest | wikidocs book 19307 raw sources
- Saved 12 chapter .txt files to raw/articles/
- Book: "바이브코딩은 왜 실패하는가" (박경태)
- Topics: 기억하는 개발, MCP 연동, 오케스트레이션, Claude Desktop/Code 활용

## [2026-04-25] create | 5 concept pages
- [[기억하는-개발.md]] — 핵심 패러다임 (기록→설계→구현→기록 사이클)
- [[오케스트레이션.md]] — 세 역할 분배와 Phase별 구현 패턴
- [[mcp-프로토콜.md]] — USB 비유, Obsidian 연동, Auto Memory의 한계
- [[에러-에스컬레이션.md]] — 3단계 레벨링, 두 번 시켜서 안 되면 지휘자에게
- [[기획-노트와-세션-로그.md]] — Obsidian 폴더 구조, 3분 세션 로그 습관

## [2026-04-25] create | 초보자 엔지니어링 사고 스킬 문서화
- [[엔지니어링-사고-초보자.md]] — 5-phase 프레임워크 (Framing/Discovery/Architecture/Roadmap/Spec)
- [[raw/papers/engineering-thinking-agent.md]] — PDF 소스 저장
- 분석 대기 GitHub 레포: gstack, gsd-2, superpowers, ralph
- Obsidian 프로젝트 구조: Ch.9 "프로젝트 먼저 노트에 저장" 구조 적용

## [2026-04-26] ingest | 세션 백업 (5 sessions)
- Discord 4 sessions + Telegram 1 session 저장
- `raw/transcripts/sessions/`에 저장
- 세션: RSS 브리핑(2), Wiki 백업 cron 설정, Discord gateway debug, Briefing test

## [2026-04-26] create | RSS 브리핑 + 세션 백업 관련 pages
- [[rss-브리핑-파이프라인.md]] — RSS 자동 수집/배송 시스템 (Telegram 고장, Discord 작동)
- [[세션-백업-cron.md]] — 매일 3:50AM 세션 wiki 보존 cron (ID: 7dc837f040a1)
- [[Discord-채널-1497196158248947842.md]] — Hermes 주요 Discord 채널
- [[Telegram-Bot-TechBriefingHermes-bot.md]] — 고장난 Telegram 브리핑 봇

## [2026-04-26] session-start | Smart video wizard
- [[raw/transcripts/sessions/20260426_discord_smart-video-wizard_20260426_093609_565126d2.md]]
- [[smart-video-wizard.md]] — concept page 틀 작성됨 (세션 진행 중)

## [2026-04-26] 프로젝트-확정 | Smart Video Speed Browser Extension
- 프로젝트 디렉토리: `Project/smart-video-speed/설계.md`
- 웹브라우저에서 재생중인 비디오 속도 조절 브라우저 확장
- [[엔지니어링-사고-초보자]] 적용 — Framing 단계 완료
- [[오케스트레이션]] 패턴: Hermes(지휘자) + execute_code+terminal(현장책임자)
- Framing 완료: Q1~Q5 모두 답변 수집
- Discovery 완료: YouTube 라이브 감지 5가지 방법, 버퍼링 감지, playbackRate 오버라이드 패턴, 라이브 속도 전환 메커니즘 정리

## [2026-04-26] 구현-완료 | 1단계: LiveSync.ts + MediaTower.ts 수정
- **GlobalSpeed 포크 기반** — GhostMode 패턴 유지
- **LiveSync.ts 신규 생성** — 라이브 감지 + 자동 속도 전환
  - currentTime < 0 → 사용자 속도 적용
  - currentTime >= 0 → playbackRate 1.0 강제
  - waiting → 即時 1x切替, playing → 사용자 속도 복귀
- **MediaTower.ts 수정** — LiveSync 통합, gsLiveMode 플래그 추가
- **SpeedSync.ts** — applySpeedToAll에서 gsLiveMode 비디오 건너뛰기
- **[[Project/smart-video-speed/설계.md]]** 갱신 완료