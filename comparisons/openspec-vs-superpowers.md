---
aliases: ["OpenSpec vs Superpowers 비교"]
type: comparison
tags: [openspec, superpowers, ai-workflow, comparison, spec-driven-development, subagent-development]
created: 2026-04-30
status: complete
---

# OpenSpec vs Superpowers 비교 분석

## 한눈에 보기

| 축 | OpenSpec | Superpowers |
|-----|----------|-------------|
| **철학** | Spec이 Source of Truth | 프로세스 품질이 코드 품질을 결정 |
| **무엇을** | 기획·설계·문서화·아카이빙 | 실행·검증·품질보장 |
| **핵심 아이디어** | Delta Specs + Artifact Graph | Subagent delegation + Two-stage review |
| **대상** | 비개발자 포함 전체 팀 | 개발자 / AI 에이전트 |
| **시작 지점** | "뭘 만들 것인가" (proposal) | "어떻게 잘 만들 것인가" (plan → execute) |
| **끝 지점** | Archive + Spec 업데이트 | 리뷰 통과 + 커밋 |

---

## 1. 철학적 차이

### OpenSpec
```
"Fluid, not rigid. Iterative, not waterfall."
→ 코드를 쓰기 전에 AI와 인간이 스펙에 합의
→ Specs/가 시스템의 Source of Truth
→ Delta Specs로 전체를 다시 쓰지 않고 변경분만 기술
```

### Superpowers
```
"Fresh subagent per task. Two-stage review every time."
→ 컨텍스트 오염을 막기 위해 작업마다 새 에이전트
→ Spec compliance review → Code quality review 순차 검증
→ TDD + 보안 스캔 + auto-fix loop
```

---

## 2. 워크플로우 비교

### OpenSpec OPSX 파이프라인
```
/opsx:propose → AI가 proposal/design/tasks 생성
/opsx:apply   → AI가 tasks.md 기반 구현
/opsx:archive → specs/ 업데이트 + changes/ 아카이브
```

### Superpowers 파이프라인
```
writing-plans          → bite-sized tasks 계획
subagent-driven-dev    → Fresh subagent per task
  ├─ implementer       → TDD로 구현
  ├─ spec reviewer     → 스펙 정합성 검증
  └─ quality reviewer  → 코드 품질·보안 검증
requesting-code-review → 최종 pre-commit 검증
```

---

## 3. 구조적 차이

### OpenSpec 디렉토리 구조
```
project/
├── openspec/
│   ├── specs/          ← 현재 시스템 명세 (Source of Truth)
│   │   └── feature-name/
│   │       └── spec.md
│   └── changes/        ← 제안된 변경사항
│       ├── feature/
│       │   ├── proposal.md
│       │   ├── design.md
│       │   ├── tasks.md
│       │   └── specs/  ← Delta Specs (ADDED/MODIFIED/REMOVED)
│       └── archive/
```

### Superpowers 아티팩트
```
project/
├── docs/
│   └── plans/          ← 구현 계획서 (bite-sized tasks)
├── src/                ← 실제 코드
└── tests/              ← 테스트 코드
```

---

## 4. 각자만의 고유 기능

### OpenSpec만의 강점
- **Delta Specs** — brownfield에서 점진적 도입 가능, 전체 재작성 불필요
- **Artifact Dependency Graph** — 아티팩트 간 의존성을 YAML로 정의, 변경 영향도 파악
- **대시보드** — 진행 상황 시각화
- **25+ AI 도구 어댑터** — Claude Code, Cursor, Windsurf, Cline, Codex 등과 통합
- **Profile Sync Drift Detection** — 스펙과 실제 코드 간 불일치 감지
- **Enterprise 확장성** — 개인 프로젝트부터 대규모 조직까지

### Superpowers만의 강점
- **Subagent Delegation** — 작업마다 새 컨텍스트, 오염 방지
- **Two-Stage Automated Review** — Spec compliance + Code quality 순차 검증
- **Security Scanning** — hardcoded secrets, injection, eval() 자동 탐지
- **Baseline-Aware Quality Gates** — 기존 실패와 신규 실패 구분
- **Auto-Fix Loop** — 검증 실패 시 자동 수정 + 재검증 (최대 2회)
- **TDD Enforcement** — RED-GREEN-REFACTOR 강제

---

## 5. 적합한 상황

| 상황 | 추천 도구 |
|------|----------|
| 새 기능 기획·설계 | OpenSpec |
| 비개발자와 협업 | OpenSpec |
| brownfield 프로젝트 도입 | OpenSpec |
| 복잡한 구현 실행 | Superpowers |
| 높은 코드 품질 요구 | Superpowers |
| 보안 중요 프로젝트 | Superpowers |
| 대규모 리팩토링 | OpenSpec (design) + Superpowers (execution) |

---

## 6. 상호보완적 통합 시나리오

```
1. OpenSpec: /opsx:propose → proposal/design/tasks 생성
2. 변환: tasks.md → Superpowers writing-plans 형식
3. Superpowers: subagent-driven-development로 구현
4. Superpowers: requesting-code-review로 최종 검증
5. OpenSpec: /opsx:archive로 스펙 업데이트 + 아카이브
```

---

## 7. 결론

> **OpenSpec은 "무엇을 할 것인가"에 강하고, Superpowers는 "어떻게 잘 할 것인가"에 강하다.**

둘은 경쟁 관계가 아니다. OpenSpec이 기획·설계·문서화의 레이어라면, Superpowers는 실행·검증·품질보장의 레이어다. 실제 프로젝트에서는 두 프레임워크를 함께 사용하는 것이 가장 강력한 AI 협업 워크플로우를 만든다.

---

## Related
- [[entities/openspec|OpenSpec]] — OpenSpec 상세 분석
- [[concepts/spec-driven-development|Spec-Driven Development]] — 개념 설명