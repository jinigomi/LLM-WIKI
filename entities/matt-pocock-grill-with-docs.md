---
title: Matt Pocock grill-with-docs
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [engineering, methodology, agent-workflow, skills, ai-assisted-development]
sources: [raw/articles/matt-pocock-skills-readme.md]
confidence: high
---

## Overview

Matt Pocock Skills 중 **가장 인기 있는 스킬**. 코딩 전 설계 단계에서 에이전트가 relentlessly 인터뷰하여 의사결정 트리의 모든 분기를 해결한다. 여기에 **공유 언어 구축** + **ADR 기록**이 더해져 [[matt-pocock-grill-me]]의 상위 버전이다.

## 핵심 차별점 (vs grill-me)

| | grill-me | grill-with-docs |
|---|---|---|
| 대상 | 비코드 태스크 | 코드 태스크 |
| 공유 언어 | 구축 안 함 | CONTEXT.md로 용어 정의 |
| 결정 기록 | 구두로만 | ADR로 문서화 |
| 지속성 | 세션 내 | 레포에 영구 저장 |

## 워크플로우

1. **그릴링** — 에이전트가 한 번에 한 질문씩 relentlessly 인터뷰. 각 질문에 대해 추천 답변 제공.
2. **코드베이스 탐색** — 답이 코드베이스에 있으면 직접 탐색 (질문 대신)
3. **용어 정의** — 새 도메인 용어 등장 시 CONTEXT.md에 기록
4. **ADR 작성** — 설명이 필요한 설계 결정은 `docs/adr/`에 기록
5. **계획 확정** — 모든 분기 해결 → 구현 시작

## 공유 언어의 힘

Matt이 "이 레포에서 가장 멋진 테크닉일 수 있다"고 말하는 이유:

```
BEFORE: "There's a problem when a lesson inside a section of a course is made 'real' (i.e. given a spot in the file system)"
AFTER:  "There's a problem with the materialization cascade"
```

- **네이밍 일관성** — 변수/함수/파일이 공유 언어를 따라 명명
- **코드베이스 탐색** — 에이전트가 더 쉽게 탐색
- **토큰 절약** — 더 적은 토큰으로 더 많은 컨텍스트 전달

## 연결 개념

- [[matt-pocock-skills]] — 이 스킬이 속한 전체 프레임워크
- [[matt-pocock-grill-me]] — 비코드 버전
- [[기억하는-개발]] — 세션 간 지식 축적. CONTEXT.md가 그 구현체 중 하나