---
title: Matt Pocock caveman
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [productivity, skills, agent-workflow, methodology]
sources: [raw/articles/matt-pocock-skills-readme.md]
confidence: high
---

## Overview

초압축 커뮤니케이션 모드. 토큰 사용량을 **~75% 절감**하면서 모든 기술적 정확성은 유지한다.

## 규칙

**삭제 대상:** 관사(a/an/the), 필러(just/really/basically/actually/simply), 예의어(sure/certainly/of course/happy to), 헤징.  
**허용:** 단편 문장, 짧은 동의어(big → extensive ❌), 일반 용어 약어(DB/auth/config/req/res/fn/impl), 화살표 인과(X → Y), 한 단어 응답.

**기술 용어는 그대로.** 코드 블록 변경 금지. 에러 메시지는 정확히 인용.

## 예시

```
Not: "Sure! I'd be happy to help you with that. The issue you're experiencing is likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"
```

## 예외: Auto-Clarity

**일시적으로 caveman 해제** — 보안 경고, 되돌릴 수 없는 작업 확인, 단편 순서가 오해 위험, 사용자 명시적 해제 요청 시. 명확한 부분 끝나면 즉시 caveman 재개.

## 지속성

한 번 트리거되면 **모든 응답에 활성화.** `"stop caveman"` 또는 `"normal mode"` 명시적 지시 전까지 유지.

## 설계 의도

장황함(verbosity)이 AI 에이전트의 가장 큰 실패 모드 중 하나. 공유 언어(CONTEXT.md)가 도메인 전문 용어로 장황함을 줄인다면, caveman은 언어 자체를 압축.

## 연결 개념

- [[matt-pocock-skills]] — 이 스킬이 속한 전체 프레임워크
- [[matt-pocock-grill-with-docs]] — 공유 언어(CONTEXT.md)로 용어 수준의 장황함 감소. caveman과 상호보완적