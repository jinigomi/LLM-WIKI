---
title: 실패 모드와 회복 전략
type: concept
tags: [harness, failure-modes, recovery, diagnosis, boundary-violation, level-mismatch]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 8. 실패 모드와 회복 전략"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# 실패 모드와 회복 전략

**하네스를 도입한 뒤에도 실패는 발생한다 — 하네스가 부족해서가 아니라, 하네스 안에서 새로운 종류의 실패가 등장하기 때문이다.** 이 실패들은 하네스의 구조가 명확한 만큼 패턴화되어 있으며, 체계적 진단과 회복이 가능하다.

## 층위 차이

| 실패 층위 | Part 1.2의 실패 | Part 8의 실패 |
|----------|----------------|--------------|
| 상태 | 하네스 도입 **전** | 하네스 도입 **후** |
| 성격 | 구조적 결함 (자기 평가 편향, 컨텍스트 불안) | 원칙 위반, 튜닝 오조정 |
| 해법 | 하네스 도입 (아키텍처) | 진단 → 복원 (운영) |

## 여섯 실패 카테고리

두 상위 축 아래 네 카테고리가 세분화된다:

```
        ┌─ 경계 침범 (8.1) ──── 8.3 Planner 실패
        │                    ├─ 8.4 Generator 실패
        │                    ├─ 8.5 Evaluator 실패
        │                    └─ 8.6 매개체 드리프트
실패 ──┤
        └─ 레벨 불일치 (8.2) ── 8.7 HITL 운영 실패
                             └─ 8.8 조직 실패
```

## 두 상위 축

### 1. 경계 침범 (Boundary Violation)

분리 원칙 위반 — 가장 흔한 실패이며, 한 번 발생하면 연쇄 효과:

| 침범 유형 | 예시 | 연쇄 결과 |
|-----------|------|----------|
| Planner → Evaluator | validation.md 작성 | Generator가 느슨한 기준으로 작업 |
| Evaluator → Planner | plan.md 읽기 | 기준이 구현 난이도에 따라 관대해짐 |
| Generator → Evaluator | validation 완화 | FAIL → PASS로 왜곡 |
| 다중 오너 | 여러 역할이 같은 파일 수정 | 매개체 드리프트 |

### 2. 레벨 불일치 (Level Mismatch)

성숙도·HITL 오판:

| 불일치 유형 | 증상 | 결과 |
|------------|------|------|
| **조기 위임** | Evaluator 미성숙 상태에서 L3 운영 | FAIL이 잡히지 않고 누적 |
| **영속적 HITL 무시** | Layer 3 Rubric 자동화 시도 | 주관적 품질 저하 |
| **모델 낙관론** | "더 좋은 모델이면 괜찮겠지"로 레벨 상승 | Part 1.2 실패의 재발 |

## 네 단계 회복 패턴

모든 실패 모드는 동일한 4단계 패턴으로 진단·회복된다:

```
1. 증상 → 2. 원인 → 3. 진단 → 4. 회복
```

- **증상**: 팀이 관찰하는 현상. "이런 일이 일어나고 있다면"
- **원인**: 어떤 원칙이 위반되었는지 구조적 설명
- **진단**: 구체적·관찰 가능한 확인 신호
- **회복**: 시간 규모(Sprint 내 / 수 Sprint / 조직적 개입)와 구체 작업

## 구체 실패 모드 (일부)

| 카테고리 | 실패명 | 핵심 증상 |
|----------|--------|----------|
| Planner | validation.md 직접 작성 | 기준이 Generator의 산출물을 전제로 후행 작성됨 |
| Generator | 불완전 상태 완료 선언 | 6단계 중 일부 생략하고 PASS 요청 |
| Evaluator | Layer 3 자동화 | Rubric 채점을 AI에게 위임 → 주관 품질 저하 |
| 매개체 | Sprint Contract 우회 | 문서 없이 구두 합의로 기준 조정 |
| HITL | 개입 형식화 | "확인했습니다"만 반복, 실질 검토 없음 |
| 조직 | 문서 과잉·비용 폭발 | 매 Sprint마다 모든 매개체를 최대 두께로 작성 |

## 회복 도구 — `/recover-*` 스킬

각 실패 모드에 대응하는 회복 스킬이 Claude Code 하네스 플러그인에 포함되어 있다. `diagnose-harness`로 분류 후 해당 `/recover-*` 스킬로 복원.

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[evaluator-role|Evaluator 역할]]
- [[delegation-levels|위임 레벨]]
- [[mediums-framework|매개체 체계]]
- [[claude-code-harness-plugin|Claude Code 하네스 플러그인]]