---
title: 하네스 엔지니어링 (Harness Engineering)
type: concept
tags: [harness, agentic-ai, gan, planner, generator, evaluator]
created: 2026-05-04
source: "wikidocs.net/book/19712 - 하네스 엔지니어링 by Sunny Park, Danny Seo"
---

# 하네스 엔지니어링

**Agentic AI 코딩에서 발생하는 구조적 실패를 방지하기 위한 아키텍처 패턴.** GAN(Generative Adversarial Network)의 Generator-Discriminator 분리 원리를 코드 생성 영역에 적용하여, Planner·Generator·Evaluator 세 역할을 분리하는 방법론.

## 핵심 원리

코드 생성과 평가를 **같은 시스템이 담당하면 자기 평가 편향(self-evaluation bias)이 구조적으로 발생**한다. 더 큰 모델을 쓰거나 더 긴 컨텍스트를 줘도 이 문제는 해결되지 않는다 — 이는 능력(ability) 문제가 아니라 **구조(structure) 문제**다.

### GAN에서 출발한 계보

- **Generator** — 노이즈에서 가짜 이미지 생성
- **Discriminator** — 진짜/가짜 판정
- **핵심 통찰**: 두 모델 분리 → 적대적 학습으로 상호 개선

이 원리를 코드 생성에 적용하면:
- 하나의 LLM이 코드를 만들고 자기가 평가 → 자기 출력에 관대해짐 → 품질 정체
- 같은 모델이 Planner·Generator·Evaluator를 모두 수행 → 역할 융합으로 편향 주입

## Harness의 구조

3역할 분리 + 11개 매개체 + 위임 레벨 프레임워크:

```
Planner ──→ Generator ──→ Evaluator
(무엇을)    (어떻게)     (통과인가)
   │            │              │
   └────────────┴──────────────┘
          11개 매개체
    (Constitution, Feature Spec, Validation, Operations 등)
```

### 세 역할

| 역할 | 책임 | 절대 하지 말아야 할 것 |
|------|------|----------------------|
| **Planner** | "무엇을" + "왜" | 구현 방법, 합격 기준 |
| **Generator** | 동작하는 증분(코드→배포→헬스체크) | validation.md 수정 |
| **Evaluator** | 합격 기준 정의 + 판정 | plan.md 읽기(무지가 엄격성을 보장) |

### 불변의 원칙 (조정 불가)

1. **세 역할의 구조적 분리** — 같은 세션에서 수행 불가
2. **매개체의 단일 오너십** — 한 파일에 한 역할만 씀
3. **두 HITL의 구분** — 과도기적 vs 영속적
4. **Evaluator의 plan.md 무지** — 구현 계획을 읽으면 기준이 관대해짐

가변 요소: 매개체의 두께, 역할의 깊이, 운영 주기

## 위임 레벨 (L1~L4)

두 축이 결정:
- **하네스 성숙도** (시간 축, 튜닝으로 증가)
- **영속적 HITL 크기** (영역 축, 도메인·위험이 결정)

| 레벨 | 설명 |
|------|------|
| L1 | 모든 단계 HITL (초기/고위험) |
| L2 | Sprint 단위 HITL |
| L3 | Feature 단위 위임 |
| L4 | 완전 위임 (내부 도구, HITL 작은 영역만) |

## Claude Code 하네스 플러그인

4 프리미티브 위에 구현:
- **Subagent** — 격리된 컨텍스트로 Planner/Generator/Evaluator 실행
- **Skill** — 워크플로 진입점(A군) + 역할 규율(B군)
- **Hook** — PreToolUse로 매개체 오너십 강제
- **Worktree** — 변경 단위 격리

GitHub: `harness-coding-plugin`

## 실패 모드 (6 카테고리)

1. **경계 침범** — 분리 원칙 위반 (가장 흔함, 연쇄 효과)
2. **레벨 불일치** — 성숙도·HITL 오판
3. **역할별 실패** (Planner/Generator/Evaluator)
4. **매개체 드리프트**
5. **HITL 운영 실패**
6. **조직 실패**

각 카테고리마다 증상→원인→진단→회복 4단계 패턴 + `/recover-*` 스킬 제공

## 관련 페이지

- [[planner-role|Planner 역할]]
- [[generator-role|Generator 역할]]
- [[evaluator-role|Evaluator 역할]]
- [[mediums-framework|매개체 체계]]
- [[delegation-levels|위임 레벨]]
- [[failure-modes|실패 모드와 회복]]
- [[claude-code-harness-plugin|Claude Code 하네스 플러그인]]