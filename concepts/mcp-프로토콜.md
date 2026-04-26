---
title: MCP (Model Context Protocol)
created: 2026-04-25
updated: 2026-04-25
type: concept
tags:
- claude-desktop
- claude-code
- mcp
- obsidian
sources:
- raw/articles/Ch.7_MCP로_세_도구_연결하기.txt
confidence: high
---

# MCP (Model Context Protocol)

## Definition
**AI가 외부 도구와 통신할 수 있게 하는 오픈 프로토콜** — Anthropic이 만든 표준 규격. "AI 세계의 USB"라는 비유로 설명된다.

## 왜 중요한가

MCP가 없던 시절:
- Obsidian에 기획문서를 작성해도 Claude Desktop은 그 문서가 있는지조차 모름
- Claude Code는 파일을 직접 읽을 수 있지만 Obsidian의 검색/링크 기능은 사용 불가
- 도구마다 다른 방식으로 연동해야 함

MCP 이후:
- 하나의 표준 프로토콜로 Obsidian, GitHub, Slack, Google Drive, 데이터베이스 등을 Claude에 연결
- **복사-붙여넣기가 사라짐**
- Claude가 필요한 부분만 직접 읽어서 [[context-rot]] 감소

## 비유: USB의 등장이전

| USB 이전 | USB 이후 (MCP 이후) |
|---------|-------------------|
| 프린터마다 다른 케이블 | 하나의 USB 포트에 모든 기기 연결 |
| 새 기기마다 호환성 확인 | 꽂으면 바로 동작 |
| 복사-붙여넣기로 데이터 전달 | AI가 직접 도구에 접근 |

## Obsidian 연결 구조

```
Obsidian (MCP 서버 역할)
    ↕ (WebSocket / HTTP-SSE)
mcp-remote (통역사: stdio ↔ HTTP/SSE)
    ↕
Claude Desktop / Claude Code
```

## 에필로그의 핵심 통찰: Auto Memory의 한계

Claude Code의 **Auto Memory**가 2026년 2월 등장했다 — 세션 간 맥락을 자동 기록.

하지만 이 책은 말한다: Auto Memory가 기록하는 것은 **"무엇을 했는가"**뿐.
- AI의 작업 로그 (빌드 명령, 코드 스타일 선호도)
- **"왜 그렇게 했는가"**와 **"다음에 뭘 해야 하는가"**는 기록하지 않음

> 저수준의 기억은 AI에게 맡기고, 고수준의 판단은 인간이 기록한다.
> 이것이 "기억하는 개발"의 완성형이다.

## Related Concepts
- [[기억하는-개발]] — Obsidian이 기억저장소로 작동
- [[context-rot]] — MCP로 컨텍스트 활용 최적화
- [[오케스트레이션]] — Claude Desktop/Code를 연결
