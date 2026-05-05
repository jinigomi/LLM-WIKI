---
title: 매개체 체계 (Mediums Framework)
type: concept
tags: [harness, mediums, constitution, feature-spec, sprint-contract, adr]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 5. 세 역할을 잇는 매개체"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# 매개체 체계

**세 역할(Planner·Generator·Evaluator)은 파일을 통해 협상한다.** 이 파일들을 '매개체(Mediums)'라 부르며, Harness의 무결성은 이 매개체들의 단일 오너십 원칙에 기반한다.

## 오너십의 원칙

**모든 매개체는 단일 오너를 가진다.** 여러 역할이 같은 파일에 동시에 쓰지 않는다.

- 모든 역할이 읽을 수 있지만, **쓰는 사람은 하나다**
- 오너십이 무너지면 드리프트 발생: A→B→C 순서로 수정되면서 어느 문장이 최신인지 불분명 → 일관된 의도 상실
- 단일 오너는 이 드리프트를 구조적으로 차단

## 매개체 분류

### Level 1 — Constitution (프로젝트 헌법)

| 파일 | 오너 | 설명 |
|------|------|------|
| `mission.md` | Planner | 누구의 무슨 문제를 왜 푸는가 |
| `tech-stack.md` | Planner | 무엇으로 만드는가, 왜 그것인가 |
| `roadmap.md` | Planner | 무슨 순서로 만드는가, 왜 그 순서인가 |

**Constitution은 자주 바뀌지 않는다.** 한 번 확정되면 그 하위의 모든 결정이 이 헌법을 전제로 움직이기 때문에, Constitution 변화는 모든 피처 스펙과 Sprint Contract의 재조정을 유발한다.

### Level 2 — Feature Spec (피처별)

| 파일 | 오너 | 설명 |
|------|------|------|
| `plan.md` | Planner | 무엇을 만들지 (구현 방법 제외) |
| `requirements.md` | Planner | 측정 가능한 합격 조건 |
| `validation.md` | Evaluator | Layer 1~3 검증 기준 |

Generator는 `requirements.md`와 `validation.md`를 동시에 입력으로 받는다.

### Level 3 — Delivery (Sprint 계약)

| 파일 | 오너 | 설명 |
|------|------|------|
| `sprint-contract.md` | Planner+Generator | 요구사항과 기준을 협상으로 확정 |
| `operations.md` (상단) | **Planner** | 아키텍처, 프로토콜, 포트 |
| `operations.md` (하단) | **Evaluator** | 헬스체크, 메트릭, SLO |
| ADR | Planner+Generator | 아키텍처 결정 기록 |

## operations.md의 이중 오너십

**유일한 예외.** 한 파일 안에서 Planner(상단: 아키텍처 정보)와 Evaluator(하단: 검증 기준)가 섹션을 나눠 소유한다. 오너십 충돌을 방지하는 메커니즘: Planner는 하단을, Evaluator는 상단을 절대 수정하지 않는다.

## Sprint Contract

기준 완화 문서가 아니라 **기준 조정을 공식화하는 문서**다.
- "이 Sprint에서는 요구사항의 이 부분을 구현하지 않습니다" → 합의된 생략
- "이 검증 기준의 Layer 3 Rubric을 이 수준으로 조정합니다" → 합의된 완화
- 조정 없는 생략/완화는 허용되지 않음

## ADR (Architecture Decision Record)

- 한 번 쓴 ADR은 수정하지 않는다 — 이전 결정을 뒤집는 새 ADR을 쓴다
- 결정의 배경('왜')이 반드시 포함되어야 한다
- Planner와 Generator가 논의하여 기록

## 매개체의 두께 조절

프로젝트 규모와 위험도에 따라 매개체의 깊이를 조절:

| 규모 | Constitution | Feature Spec | Delivery |
|------|-------------|-------------|----------|
| 개인/소형 | 1페이지 mission | 간략한 plan | Sprint Contract는 구두 합의 |
| 중형 | 3종 전체 | plan + requirements | operations.md + Sprint Contract |
| 대형/규제 | 3종 + glossary | 전체 + traceability | ADR + operations + 모든 Contract 문서화 |

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[planner-role|Planner 역할]]
- [[generator-role|Generator 역할]]
- [[evaluator-role|Evaluator 역할]]
- [[delegation-levels|위임 레벨]]
- [[failure-modes|실패 모드와 회복]]