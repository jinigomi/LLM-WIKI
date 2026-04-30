---
title: Matt Pocock grill-me
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [engineering, methodology, agent-workflow, skills, productivity]
sources: [raw/articles/matt-pocock-skills-readme.md]
confidence: high
---

## Overview

비코드 태스크를 위한 relentlessly 인터뷰 스킬. 설계 의사결정 트리의 모든 분기를 하나씩 해결할 때까지 질문한다. 한 번에 한 질문만, 각 질문에 추천 답변 제공.

[[matt-pocock-grill-with-docs]]의 단순 버전 — 코드 컨텍스트 + ADR + 공유 언어가 빠짐.

## 핵심 원칙

1. **한 번에 한 질문**
2. **각 질문에 추천 답변 제공**
3. **코드베이스에서 답을 찾을 수 있으면 질문 대신 탐색**
4. **모든 분기 해결 시까지 relentlessly**

## 설계 이유

> "아무도 자기가 정확히 무엇을 원하는지 모른다" — The Pragmatic Programmer

Misalignment가 소프트웨어 개발에서 가장 흔한 실패 모드. AI 시대에도 마찬가지. 에이전트가 당신의 진짜 의도를 완전히 이해할 때까지 그릴링해야 방향을 틀지 않는다.

## Matt의 조언

**매번 변경을 할 때마다 사용하라** — grill-me나 grill-with-docs 없이 코딩을 시작하지 말 것.

## 연결 개념

- [[matt-pocock-skills]] — 이 스킬이 속한 전체 프레임워크
- [[matt-pocock-grill-with-docs]] — 코드 태스크 + 공유 언어 + ADR 버전
- [[spec-driven-development]] — Spec을 먼저 합의하는 접근. 그릴링은 spec 도출의 도구가 될 수 있음