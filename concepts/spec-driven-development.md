---
title: Spec-Driven Development
created: 2026-04-30
updated: 2026-04-30
type: concept
tags: [spec-driven, AI-collaboration, engineering-thinking, agent-workflow, methodology, software-development]
sources: [https://github.com/Fission-AI/OpenSpec]
confidence: high
---

# Spec-Driven Development (명세 기반 개발)

## 정의
**코드를 작성하기 전에 인간과 AI가 명세(Spec)에 합의하는 개발 방법론.** AI 코딩 환경에서 요구사항이 채팅 히스토리에만 존재할 때 발생하는 불확실성과 비결정성을 해소하기 위해 등장했다.

## 왜 필요한가
AI 코딩 어시스턴트는 강력하지만, 요구사항이 채팅에만 존재할 때 예측 불가능하다.
- "다크모드 추가해줘" → 어떤 다크모드? 토글? 자동감지? 스코프?
- Spec-Driven Development는 **구현 전에 무엇을 만들지 합의**한다.

## 핵심 원칙 (from OpenSpec)
1. **Fluid, not rigid** — 페이즈 게이트 없음. 필요한 걸 순서 상관없이 작업
2. **Iterative, not waterfall** — 만들면서 배우고 배우면서 개선
3. **Easy, not complex** — 최소한의 세팅, 최소한의 세레모니
4. **Brownfield-first** — 기존 코드베이스에서도 작동 (대부분의 현실 작업)

## OpenSpec 모델

### 아티팩트 흐름
```
proposal → specs → design → tasks → implement
   ↓         ↓        ↓        ↓
  왜?     무엇을?   어떻게?   단계별로
```

### Delta Specs: 브라운필드의 핵심
명세의 전체 재작성이 아니라 **변경사항(Delta)만** 기술:
- `ADDED` — 신규 요구사항
- `MODIFIED` — 기존 변경
- `REMOVED` — 제거

이 방식으로:
- 명확성: 정확히 무엇이 바뀌는지
- 충돌 회피: 같은 파일을 수정해도 다른 요구사항이면 충돌 없음
- 리뷰 효율: 변경된 부분만 확인

### 의존성이 게이트가 아니다
```
proposal (루트)
   ├── specs
   ├── design
   └── tasks ← specs + design 완료 후 가능
```
의존성은 "무엇을 다음에 해야 하는지"가 아니라 **"무엇이 가능한지"** 를 보여준다. 디자인을 스킵하거나, 스펙을 먼저 또는 나중에 만들 수 있다.

## Spec vs Implementation
좋은 스펙은 **행동 계약(behavior contract)** 이다. 구현 계획이 아니다.
- ✅ 관찰 가능한 행동, 입력/출력, 에러 조건
- ❌ 내부 클래스명, 라이브러리 선택, 구현 세부사항

질문: "구현을 변경해도 외부에서 보이는 동작이 변하지 않는다면, 그건 스펙에 들어갈 내용이 아니다."

## AI 협업에서의 역할

### Agentic Workflow
OpenSpec의 OPSX 워크플로우에서 AI는:
1. `/opsx:propose` → proposal + specs + design + tasks 생성
2. `/opsx:apply` → tasks.md의 체크리스트 순차 구현
3. `/opsx:archive` → delta specs를 메인 스펙에 병합, 변경사항 아카이빙

### Human + Agent Loop
```
Human provides intent/cotext/constraints
   → Agent drafts behavior-first requirements + scenarios
   → Agent keeps implementation detail in design.md/tasks.md, not spec.md
   → Validation confirms structure before implementation
```

## 다른 Spec 프레임워크와 비교

### SDD 구현체 스펙트럼

| | OpenSpec | cc-sdd | Don Cheli |
|---|---|---|---|
| 무게 | 경량 | 중간 | 중량 |
| 설치 | `npm i -g` | `npx cc-sdd@latest` | `npm i -g` + `don-cheli install` |
| 에이전트 | 25+ | 8개 | 7개 |
| 격리 | 없음 | Agent Skills (프로세스) | Docker + worktree |
| TDD | 선택 | 선택 (/kiro-impl 내장) | **강제** (게이트 통과 필수) |
| 설계 철학 | Fluid, iterative | Boundary-first | Docker-wrapped pipeline |
| 브라운필드 | Delta specs | 가능 | 가능 |
| 언어 | TypeScript (Node) | TypeScript (Node) | TypeScript (Node) |
| 라이선스 | Apache 2.0 | MIT | Apache 2.0 |
| God Node | — | runPlanExecution | orchestrator.ts |

### 핵심 차이

| | OpenSpec | Spec Kit (GitHub) | Kiro (AWS) |
|---|---|---|---|
| 무게 | 경량 | 중량 (Phase gate) | 중간 |
| 언어 | TypeScript (Node) | Python | — |
| 유연성 | Fluid, iterative | Rigid phases | — |
| 도구 락인 | 없음 (25+ 도구) | 없음 | Kiro IDE 전용 |
| 모델 제한 | 없음 | 없음 | Claude 전용 |
| 브라운필드 | Delta specs | — | — |

## [[Vibe Coding Blueprint Pattern]]과의 관계
Blueprint 패턴은 "코드보다 설계를 먼저 AI에게 시키는" 접근법이다. Spec-Driven Development는 Blueprint 패턴의 정형화된 버전이라고 볼 수 있다. `proposal → specs → design → tasks` 는 Blueprint의 `설계 → 구현` 패턴과 공명한다.

## 관련 페이지
- [[openspec]] — OpenSpec 도구 자체
- [[subagent-driven-development]] — 멀티 에이전트 병렬 구현 패턴
- [[vibe-coding-blueprint-pattern]] — 블루프린트 패턴
- [[엔지니어링-사고-초보자]] — 초보자 엔지니어링 사고