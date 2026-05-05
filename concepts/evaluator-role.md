---
title: Evaluator 역할
type: concept
tags: [harness, evaluator, agentic-ai, validation, rubric, tdd]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 4. Evaluator"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# Evaluator 역할

**하네스 성숙도는 곧 Evaluator 성숙도다.** Planner와 Generator는 Evaluator가 제시하는 기준을 전제로 움직인다. 기준이 관대하면 Generator의 조기 완료가 통과되고, 기준이 모호하면 Planner의 의도가 전달되지 않는다. Evaluator는 수동적 채점자가 아니라 **능동적 기준 설정자**다.

## 두 시점

Evaluator는 Sprint 내내 두 시점에 등장한다:

| 시점 | 작업 | 산출물 |
|------|------|--------|
| **Sprint 전** | 합격 기준 작성 (더 중요) | `validation.md`, `operations.md` 하단 |
| **Sprint 후** | 판정 | PASS/FAIL + 실패 분석 |

**Sprint 전 작업이 Evaluator의 핵심이다.** Generator가 향해 갈 목표를 Evaluator가 먼저 확정해둔다 — TDD의 "테스트 먼저"가 하네스 수준에서 나타나는 방식.

## Evaluator의 무지(無知) 원칙

**Evaluator는 `plan.md`를 읽지 않는다.** 구현 방식을 모르는 채로 기준을 써야 엄격해진다. plan.md를 읽으면 "이 기능은 구현이 복잡하니까 기준을 낮춰야지" 같은 무의식적 관대함이 생긴다. 이 무지는 결함이 아니라 **설계된 특성**이다.

## 3-Layer 검증 체계

```
Layer 1: 운영·DevOps
  → CI 통과, 헬스체크, SLO, 배포 성공
  → 자동화 가능 (기계 판정)

Layer 2: 기능·TDD
  → requirements.md의 모든 항목 충족
  → 테스트 케이스로 검증 가능 (기계 판정)

Layer 3: 주관적 Rubric
  → 코드 품질, 설계 적절성, UX 자연스러움
  → AI가 자기 채점할 수 없음 — 영속적 HITL의 핵심
```

**Layer 3는 Rubric을 통해 정성적 판단을 구조화한다.** Rubric은 5점 척도로 각 평가 항목의 수준을 정의하며, Evaluator는 이 Rubric에 따라 채점한다.

## Evaluator의 네 가지 자산

Evaluator가 튜닝되면서 축적하는 4가지 지속 자산:

1. **놓쳤던 실패 사례** — PASS 판정 후 발견된 결함. 다음 validation.md 작성 시 이 유형을 새 체크리스트로 추가
2. **Rubric 개정 이력** — 어떤 항목이 추가/제거/가중치 변경됐는지의 기록
3. **Edge case 라이브러리** — 프로젝트 도메인 고유의 예외 상황 목록
4. **반례 카탈로그** — "이런 상황에서 Generator는 이렇게 회피하려 한다"의 패턴 집합

## Evaluator 튜닝 원칙

- **회의적으로 튜닝하라** — "Generator가 이 기준을 통과 못 할까?"가 아니라 "Generator가 이 기준을 어떻게 우회할까?"를 상상
- **판정이 쌓일수록 하네스가 강해진다** — 매 Sprint의 FAIL 판정은 하네스 개선의 원료
- **PASS보다 FAIL이 더 가치 있다** — PASS는 현재 하네스로 잡을 수 있는 범위를 확인할 뿐, FAIL은 하네스가 커버하지 못하는 영역을 보여줌

## HITL 지점

- **Layer 3 Rubric 채점** — 영속적 HITL 영역 (AI 자체 평가 불가)
- **validation.md 초안 검토** — Generator 실행 전 사람이 기준의 엄격성 확인
- **반례 카탈로그 업데이트** — 사람이 새로운 우회 패턴 발견 시 추가

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[planner-role|Planner 역할]]
- [[generator-role|Generator 역할]]
- [[mediums-framework|매개체 체계]]
- [[delegation-levels|위임 레벨]]
- [[failure-modes|실패 모드와 회복]]