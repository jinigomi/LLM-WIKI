---
title: Matt Pocock TDD
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [engineering, methodology, agent-workflow, skills, testing, ai-assisted-development]
sources: [raw/articles/matt-pocock-skills-readme.md]
confidence: high
---

## Overview

Matt Pocock의 Red-Green-Refactor TDD 스킬. 기존 TDD의 핵심 원칙을 AI 에이전트에 최적화하여 **수직 슬라이스**, **인터페이스 기반 테스트**, **안티패턴 명시적 금지**를 강제한다.

## 철학

### 좋은 테스트
- **공개 인터페이스로 행동 검증** — 구현이 완전히 바뀌어도 테스트는 살아남아야 함
- **통합 테스트 지향** — 실제 코드 경로를 public API로 테스트
- **명세(specification)처럼 읽힘** — "user can checkout with valid cart" 식

### 나쁜 테스트
- 내부 collaborator 모킹
- private 메서드 테스트
- 데이터베이스 직접 쿼리 등 인터페이스 외부 검증
- **경고 신호:** 동작이 안 변했는데 리팩터링 후 테스트가 깨짐

## 수직 vs 수평 슬라이스

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
```

수평 슬라이스의 문제:
- **상상된 동작을 테스트** — 실제 동작이 아닌 가상의 동작
- **데이터 구조의 '형태'만 테스트** — 사용자 행동이 아닌 함수 시그니처
- **실제 변화에 둔감** — 동작이 깨져도 통과, 괜찮은데 실패
- **헤드라이트를 넘어 질주** — 구현을 이해하기 전에 테스트 구조를 커밋

## 참조 자료

스킬 디렉토리에 포함된 상세 가이드:
- `tests.md` — 좋은/나쁜 테스트 예시
- `mocking.md` — 모킹 가이드라인
- `deep-modules.md` — 깊은 모듈 설계 원칙
- `interface-design.md` — 인터페이스 설계 원칙
- `refactoring.md` — 리팩터링 원칙

## 연결 개념

- [[matt-pocock-skills]] — 이 스킬이 속한 전체 프레임워크
- [[matt-pocock-improve-codebase-architecture]] — TDD로 만든 코드의 아키텍처 개선
- [[matt-pocock-diagnose]] — TDD로 예방하지 못한 버그의 디버깅
- [[openspec]] — Spec-first 접근. TDD와는 다른 축 (spec → test → code)