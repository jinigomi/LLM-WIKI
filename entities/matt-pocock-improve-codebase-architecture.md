---
title: Matt Pocock improve-codebase-architecture
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [engineering, methodology, agent-workflow, skills, ai-assisted-development, architecture]
sources: [raw/articles/matt-pocock-skills-readme.md]
confidence: high
---

## Overview

코드베이스가 **진흙 덩어리(ball of mud)**가 되는 것을 방지하는 Matt Pocock의 핵심 스킬. **Deepening opportunities** — 얕은(shallow) 모듈을 깊은(deep) 모듈로 리팩터링할 기회를 찾는다.

## 핵심 용어 (John Ousterhout, A Philosophy of Software Design 기반)

| 용어 | 정의 |
|------|------|
| **Module** | 인터페이스 + 구현을 가진 모든 것 (함수, 클래스, 패키지, 슬라이스) |
| **Interface** | 호출자가 알아야 할 모든 것 — 타입, 불변성, 에러 모드, 순서, 설정 |
| **Implementation** | 내부 코드 |
| **Depth** | 인터페이스의 레버리지: 작은 인터페이스 뒤에 많은 동작 |
| **Deep** | 높은 레버리지 |
| **Shallow** | 인터페이스가 구현만큼 복잡함 |
| **Seam** | 인터페이스가 존재하는 위치. in-place 편집 없이 동작 변경 가능 |
| **Adapter** | seam에서 인터페이스를 충족하는 구체적 구현 |
| **Leverage** | 호출자가 depth에서 얻는 이점 |
| **Locality** | 유지보수자가 depth에서 얻는 이점 — 변경/버그/지식이 한 곳에 집중 |

## 프로세스

### 1. Explore
CONTEXT.md(도메인 용어)와 ADR(기존 설계 결정)을 먼저 읽은 후, 유기적으로 코드베이스 탐색:

- 하나를 이해하려고 여러 작은 모듈 사이를 왔다 갔다 하는가?
- 모듈이 **얕은가** — 인터페이스가 구현만큼 복잡?
- 순수 함수를 테스트 용이성만 위해 추출했지만 실제 버그는 호출 방식에 숨어 있는가?
- 강결합 모듈이 seam을 넘어 새고 있는가?
- 어떤 부분이 테스트 불가능하거나 현재 인터페이스로 테스트하기 어려운가?

### 2. 삭제 테스트 (Deletion Test)
의심되는 모듈에 적용: 삭제했을 때 복잡성이 사라지면 pass-through. 복잡성이 N개 호출자에 재분산되면 가치 있는 모듈.

### 3. 후보 제시
번호 매겨진 리스트로 deepening 기회 제시. 각 항목:
- 관련 파일/모듈
- 현재 아키텍처가 friction을 일으키는 이유
- 무엇을 바꿀지 (plain English)
- 이점 (locality + leverage + 테스트 개선 관점)

**CONTEXT.md 용어로 도메인을, LANGUAGE.md 용어로 아키텍처를 설명.** ADR과 충돌하면 명시적으로 표시.

## 실행 주기

**며칠에 한 번** 실행 권장. AI가 코드를 빠르게 생성할수록 엔트로피도 빠르게 쌓이기 때문.

## 연결 개념

- [[matt-pocock-skills]] — 이 스킬이 속한 전체 프레임워크
- [[matt-pocock-tdd]] — TDD로 만든 코드의 아키텍처 개선
- matt-pocock-zoom-out — 전체 시스템 맥락에서 코드 이해 (개선 전 탐색 단계) (별도 페이지 없음)
- [[기억하는-개발]] — 세션 간 지식 축적. CONTEXT.md가 아키텍처 이해의 기반