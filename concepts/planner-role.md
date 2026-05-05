---
title: Planner 역할
type: concept
tags: [harness, planner, agentic-ai, constitution, requirements]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 2. Planner"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# Planner 역할

**세 역할 중 가장 먼저 일하는 역할. "무엇을 만들지"와 "왜"를 결정한다.** Planner의 출력은 구현 방법이나 합격 기준을 포함하지 않는다 — 그 경계를 넘는 모든 문장은 다른 역할에 편향을 주입한다.

## Planner의 입력 — 작은 입력, 거대한 출력 공간

사용자 입력은 극도로 짧다 ("일기 앱 만들어줘" = 8글자). 짧은 입력일수록 에이전트는 훈련 데이터의 흔한 패턴으로 빈틈을 메우며, 이 확장이 사용자 의도와 일치할 확률은 낮다.

→ **Planner의 첫 작업은 입력을 늘리는 것.** Constitution 인터뷰로 맥락을 끌어낸다.

### 두 가지 시작점

| 유형 | 상황 | 출력 |
|------|------|------|
| **Greenfield** | 프로젝트 미존재 | Constitution 3종 전체 + 인터뷰 |
| **Brownfield** | 프로젝트 존재, Constitution 확정 | Feature Spec 일부 |

## Planner의 출력

### 1. Constitution (프로젝트 수준, 단 한 번)

| 파일 | 내용 |
|------|------|
| 🏛️ `mission.md` | 누구의 무슨 문제를 왜 푸는가 |
| 🏛️ `tech-stack.md` | 무엇으로 만드는가, 왜 그것인가 |
| 🏛️ `roadmap.md` | 무슨 순서로 만드는가, 왜 그 순서인가 |

**검증**: "이것을 만들지 않겠다" 목록이 함께 있는지 확인. 배제가 없으면 mission은 모든 피처 요청을 수용하는 고무줄이 된다.

### 2. Feature Spec (피처 단위, 피처마다 새로 작성)

| 파일 | 내용 |
|------|------|
| 📋 `plan.md` | 무엇을 만들지 (구현 방법 제외) |
| 📋 `requirements.md` | 측정 가능한 합격 기준 |

## Planner가 절대 하면 안 되는 것

1. **validation.md를 쓰지 않는다** — Planner에게 낙관 편향이 있어 기준이 느슨해짐. validation은 Evaluator가 별도 세션에서 작성
2. **구현 방법(how)을 쓰지 않는다** — 언어/프레임워크 선택은 Constitution에서 이미 완료. 스택 내 구현 전략은 Generator의 결정
3. **operations.md 하단의 헬스체크·메트릭을 쓰지 않는다** — Evaluator 영역

## HITL 지점

Planner 출력에 사람 개입이 필요한 지점:
- **Constitution 인터뷰** — 가장 대표적인 영속적 HITL 사례
- **mission.md 검토** — 배제 조건 명확한지 확인
- **requirements.md 검토** — "안정적", "빠르게" 같은 모호한 기준 → 측정 가능한 문장으로 변경

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[generator-role|Generator 역할]]
- [[evaluator-role|Evaluator 역할]]
- [[mediums-framework|매개체 체계]]