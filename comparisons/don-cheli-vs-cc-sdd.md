---
title: Don Cheli vs cc-sdd — SDD 프레임워크 비교
created: 2026-04-30
updated: 2026-04-30
type: comparison
tags: [spec-driven-development, agent-framework, comparison, sdlc]
sources: [https://github.com/doncheli/don-cheli-sdd, https://github.com/gotalab/cc-sdd]
confidence: high
---

둘 다 **Spec-Driven Development (SDD)** 를 구현하는 TypeScript 기반 오픈소스 프레임워크지만, 설계 철학과 동작 방식이 근본적으로 다르다.

## 한눈에 비교

| | Don Cheli | cc-sdd |
|---|---|---|
| **저자** | @DonCheli | @gotalab |
| **라이선스** | Apache 2.0 | MIT |
| **슬로건** | "Stop guessing. Start engineering." | "Boundaries are not overhead." |
| **Graphify** | 138 노드·212 엣지·12 커뮤니티 | 214 노드·340 엣지·44 커뮤니티 (빈 커뮤니티 37개) |
| **지원 에이전트** | 7개 | 8개 |
| **설치** | `npm i -g` + `don-cheli install` | `npx cc-sdd@latest` 한 줄 |

## 설계 철학

```text
          OpenSpec (경량)
              ↑
        "인간+AI가 합의만 하면 OK"
              │
    ┌─────────┼─────────┐
    │                    │
cc-sdd (중간)     Don Cheli (중량)
"경계를 명시적으로"       "기계가 강제한다"
스킬 기반               Docker 격리
에이전트 자율성 ↑         에이전트 자율성 ↓
```

### Don Cheli — "기계가 강제하는 SDD"
- **철학:** AI는 스스로 판단하지 않는다. 파이프라인이 판단한다.
- **격리:** Git worktree + Docker 컨테이너. 실패한 코드는 실제 프로젝트에 절대 닿지 않음
- **TDD:** 선택이 아니라 게이트. 각 단계마다 테스트 통과가 강제됨
- **거버넌스:** `.dc/gates/*.yml`로 사용자 정의 게이트. `orchestrate()`가 모든 걸 통제

### cc-sdd — "경계를 명시적으로 만드는 SDD"
- **철학:** AI는 경계를 주면 자율적으로 움직일 수 있다. 스펙은 계약이다.
- **구현:** `/kiro-impl`에서 새 작업자 + 독립 리뷰어 + auto-debug 조합
- **진입 장벽:** npx 한 줄. 에이전트 스킬은 Markdown 파일
- **거버넌스:** `_Boundary:_` + `_Depends:_` 어노테이션으로 태스크 경계. `design.md`의 File Structure Plan

## 워크플로우 단계

| 단계 | Don Cheli | cc-sdd |
|---|---|---|
| 아이디어 입력 | `don-cheli "기능 설명"` | `/kiro-discovery "아이디어"` |
| 명세 | `/dc:specify` → Gherkin 시나리오 | `/kiro-spec-requirements` → EARS 형식 |
| 명확화 | `/dc:clarify` → 가상 QA | discovery 단계에서 자동 분류 |
| 설계 | `/dc:tech-plan` | `/kiro-spec-design` → Mermaid 다이어그램 |
| 태스크 분해 | `/dc:breakdown` | `/kiro-spec-tasks` → 의존성 그래프 |
| 구현 | `/dc:implement` → TDD 강제 | `/kiro-impl` → TDD + 독립 리뷰 |
| 리뷰 | `/dc:review` → 7차원 피어 리뷰 | `/kiro-impl` 내장 (auto-debug) |

## 언제 무엇을 선택할까

**Don Cheli:** 프로젝트 품질이 절대적으로 중요한 경우. Docker 사용 가능 + TDD 강제가 필요 + 팀에 시니어 엔지니어가 있을 때.

**cc-sdd:** 빠른 시작 + 유연성이 필요할 때. 여러 에이전트를 시험 중이거나, 경량 셋업을 선호하는 팀.

**둘 다:** OpenSpec보다 더 강한 거버넌스가 필요하지만, 각기 다른 방식으로 구현함.

## 공통점

- TypeScript (Node.js) 기반
- `specify → design → tasks → implement` 패턴
- 다중 AI 에이전트 지원
- Markdown으로 관리되는 컨텍스트 계약
- TDD 통합

## 연결 개념

- [[spec-driven-development]] — 상위 개념
- [[openspec]] — 경량 SDD의 대표주자
- [[don-cheli-sdd]] — Don Cheli 상세
- [[cc-sdd]] — cc-sdd 상세
- [[harness-engineering]] — AI 행동 제어의 상위 패러다임