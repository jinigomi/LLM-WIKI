---
title: Generator 역할
type: concept
tags: [harness, generator, agentic-ai, sprint, validation]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 3. Generator"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# Generator 역할

**산출물은 코드가 아니라 "동작하는 증분(working increment)"이다.** Generator는 코드를 작성하는 것으로 끝나지 않는다 — 배포, 헬스체크, CI 통과까지 완료해야 한다.

## 6단계 완료 체인

하나라도 빠지면 Generator의 일은 끝나지 않았다:

```
1. 코드 작성
   ↓
2. 테스트 통과 (Unit/Integration/E2E 모두)
   ↓
3. 로컬 빌드 (배포용 빌드)
   ↓
4. CI 파이프라인 통과 (린트, 보안 스캔 포함)
   ↓
5. 스테이징 배포
   ↓
6. 헬스체크 통과 (실제 요청 처리, SLO 내 응답)
```

**부분 Green은 Green이 아니다** — 하나라도 실패하면 Sprint 미완료. Generator는 실패하는 테스트를 임시로 비활성화하거나 skip 처리하지 않는다.

## Generator의 입력

1. **`requirements.md`** (Planner) — 무엇을 만들어야 하는지
2. **`validation.md`** (Evaluator) — 어떤 기준으로 평가될지

Generator는 두 문서를 동시에 읽어야 한다. requirements만 읽으면 합격 기준을 모르고, validation만 읽으면 목표를 모른다.

## Generator의 경계

| 해야 하는 것 | 하면 안 되는 것 |
|-------------|----------------|
| Red(실패하는 테스트)에서 시작해 Green으로 종료 | validation.md 수정 (기준은 협상 전 이미 확정) |
| 컨텍스트 길어지면 리셋/컴팩션 | 실패 테스트 skip/비활성화 |
| `operations.md` 하단 읽기 (헬스체크 파악) | 불완전한 상태를 "완료"로 선언 |
| Sprint 종료 시 Evaluator에게 평가 요청 | 배포 없이 "로컬에서 돌아가니까 됐다" |

## HITL 지점

- **Sprint 시작**: Generator가 requirements + validation을 이해했는지 확인
- **Sprint 종료**: 6단계 완료 체인 결과 검토
- **레벨에 따라**: L1은 매 Sprint HITL, L3 이상은 Feature 단위 위임

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[planner-role|Planner 역할]]
- [[evaluator-role|Evaluator 역할]]
- [[mediums-framework|매개체 체계]]