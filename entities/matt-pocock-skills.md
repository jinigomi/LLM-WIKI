---
title: Matt Pocock Skills
created: 2026-04-30
updated: 2026-05-02
type: entity
tags: [agent-workflow, skills, engineering, software-development, methodology, ai-assisted-development, productivity]
sources: [raw/articles/matt-pocock-skills-readme.md, raw/articles/matt-pocock-skills-context.md]
confidence: high
---

## Overview

Matt Pocock의 **Skills For Real Engineers** — AI 코딩 에이전트를 위한 실용적이고 조합 가능한 스킬 모음. "Vibe Coding"이 아닌 **실제 엔지니어링**을 목표로 설계되었다. GSD, BMAD, Spec-Kit 등이 프로세스를 소유하면서 통제권을 빼앗는 것과 달리, 이 스킬들은 **작고 쉽게 커스터마이즈**할 수 있으며 어떤 모델과도 작동한다.

60,000명 이상의 구독자를 가진 [Total TypeScript 뉴스레터](https://www.aihero.dev/s/skills-newsletter)로 업데이트를 제공한다.

**설치:** `npx skills@latest add mattpocock/skills`

---

## 철학: 4대 실패 모드 해결

Matt은 AI 코딩 에이전트(Claude Code, Codex 등)에서 반복되는 4가지 실패 모드를 식별하고 각각에 대응하는 스킬을 제공한다.

### #1: 에이전트가 내 의도와 달랐다 (Misalignment)

> "자신이 정확히 무엇을 원하는지 아는 사람은 아무도 없다" — The Pragmatic Programmer

**원인:** 에이전트와 사용자 간 **커뮤니케이션 갭**.  
**해결:** **그릴링 세션** — 에이전트가 당신에게 상세한 질문을 하도록 강제.

- [[matt-pocock-grill-me]] — 비코드 태스크용
- [[matt-pocock-grill-with-docs]] — 코드 태스크 + 공유 언어 구축 + ADR

### #2: 에이전트가 너무 장황하다 (Verbosity)

> "보편 언어(ubiquitous language)로 개발자 간 대화와 코드 표현이 모두 동일한 도메인 모델에서 파생된다" — Domain-Driven Design

**원인:** 에이전트가 프로젝트 전문 용어(jargon)를 이해하지 못해 20단어로 설명할 것을 1단어로 못 줄임.  
**해결:** **공유 언어(shared language)** — `CONTEXT.md` (+ ADR)로 도메인 용어를 정의하여 커뮤니케이션을 압축.

| Before | After |
|--------|-------|
| "There's a problem when a lesson inside a section of a course is made 'real'" | "There's a problem with the **materialization cascade**" |

**이점:** 일관된 네이밍 → 코드베이스 탐색 용이 → 에이전트 토큰 절약.

### #3: 코드가 작동하지 않는다 (Broken Code)

> "피드백 속도가 속도 한계다" — The Pragmatic Programmer

**원인:** 피드백 루프 부재. 정적 타입, 브라우저 접근, 자동화 테스트 없이 에이전트가 날아다니는 것.  
**해결:**
- [[matt-pocock-tdd]] — Red-Green-Refactor 루프, 수직 슬라이스 방식, 통합 테스트 지향
- matt-pocock-diagnose — 재현→최소화→가설→측정→수정→회귀 테스트의 디버깅 루프 (별도 페이지 없음)

### #4: 진흙 덩어리가 되었다 (Ball of Mud)

> "매일 시스템 설계에 투자하라" — Kent Beck  
> "최고의 모듈은 깊다(deep). 단순한 인터페이스로 많은 기능에 접근할 수 있다" — A Philosophy of Software Design

**원인:** AI가 코딩 속도를 급진적으로 높이면서 소프트웨어 엔트로피도 가속화. 코드베이스가 전례 없는 속도로 복잡해짐.  
**해결:** 코드 설계에 대한 **급진적 관심**:

- matt-pocock-to-prd — 모듈 구조 고려한 PRD 생성 (별도 페이지 없음)
- matt-pocock-zoom-out — 전체 시스템 맥락에서 코드를 설명하도록 지시 (별도 페이지 없음)
- [[matt-pocock-improve-codebase-architecture]] — 코드베이스의 deepening 기회 발굴 (며칠에 한 번 실행 권장)

---

## 스킬 카탈로그

### Engineering (일일 코딩 작업)

| 스킬 | 설명 |
|------|------|
| diagnose | 재현→최소화→가설→측정→수정→회귀 테스트 루프 |
| grill-with-docs | 도메인 모델과 함께 계획을 그릴링 + CONTEXT.md/ADR 업데이트 |
| triage | 상태 머신 기반 이슈 트리아지 (bug/enhancement × 5개 상태) |
| improve-codebase-architecture | 코드베이스의 deepening 기회 발굴 (삭제 테스트, 모듈 깊이 분석) |
| setup-matt-pocock-skills | 레포별 설정 스캐폴딩 (이슈 트래커, 트리아지 라벨, 도메인 문서 경로) |
| tdd | Red-Green-Refactor + 수직 슬라이스 TDD. 모킹, 깊은 모듈 가이드 포함 |
| to-issues | 계획/스펙/PRD를 독립적으로 작업 가능한 GitHub 이슈로 분할 |
| to-prd | 대화 컨텍스트 → PRD 생성 → GitHub 이슈 등록 |
| zoom-out | 낯선 코드 영역을 전체 시스템 맥락에서 설명 |

### Productivity (범용 워크플로우)

| 스킬 | 설명 |
|------|------|
| caveman | 초압축 커뮤니케이션 모드. 토큰 사용량 ~75% 절감 |
| grill-me | 설계 의사결정 트리의 모든 분기를 해결할 때까지 relentlessly 인터뷰 |
| write-a-skill | 점진적 공개(progressive disclosure) + 번들 리소스로 새 스킬 생성 |

### Misc (가끔 사용)

| 스킬 | 설명 |
|------|------|
| git-guardrails-claude-code | Claude Code 훅으로 위험한 git 명령어(push, reset --hard) 차단 |
| migrate-to-shoehorn | `as` 타입 assertion → @total-typescript/shoehorn 마이그레이션 |
| scaffold-exercises | 연습문제 디렉토리 구조 생성 |
| setup-pre-commit | Husky pre-commit 훅 + lint-staged + Prettier + 타입 체크 + 테스트 |

### Personal (Matt 개인 설정용, 플러그인에서 비활성)

| 스킬 | 설명 |
|------|------|
| edit-article | 문서 구조 조정, 명확성 개선, 산문을 다듬어 글 편집 및 개선 |
| obsidian-vault | Obsidian vault 내 노트 검색, 생성, 관리 (wikilinks + 인덱스 노트) |

### Deprecated (더 이상 사용하지 않음)

| 스킬 | 설명 |
|------|------|
| design-an-interface | 병렬 서브에이전트로 모듈의 급진적으로 다른 인터페이스 설계 생성 |
| qa | 사용자가 버그를 대화형으로 보고하고 에이전트가 GitHub 이슈로 등록하는 QA 세션 |
| request-refactor-plan | 사용자 인터뷰로 상세 리팩터 계획 생성 후 GitHub 이슈로 등록 |
| ubiquitous-language | 현재 대화에서 DDD 스타일의 ubiquitous language 용어집 추출 |

---

## 공유 언어 (CONTEXT.md)

Matt의 모든 스킬을 관통하는 핵심 인프라. 프로젝트의 도메인 용어 사전 역할:

| 용어 | 정의 | 회피할 단어 |
|------|------|------------|
| **Issue tracker** | 이슈를 호스팅하는 도구 (GitHub Issues, Linear, 로컬 `.scratch/` 마크다운 등). `to-issues`, `to-prd`, `triage`, `qa` 등이 읽고 쓰는 대상 | backlog manager, backlog backend, issue host |
| **Issue** | **Issue tracker** 내 단일 작업 단위 — 버그, 태스크, PRD, 또는 `to-issues`가 생성한 슬라이스 | ticket (외부 시스템이 ticket이라 부를 때만 예외) |
| **Triage role** | 트리아지 중 **Issue**에 적용되는 표준 상태 머신 라벨 (예: `needs-triage`, `ready-for-agent`). 각 role은 `docs/agents/triage-labels.md`를 통해 실제 라벨 문자열로 매핑됨 | — |

**Flagged ambiguities (과거 해결된 용어 충돌):**
- "backlog" — 과거에는 이슈를 호스팅하는 *도구*와 그 안의 *작업*을 모두 의미했음. 해결: 도구는 **Issue tracker**, "backlog"는 더 이상 도메인 용어로 사용하지 않음.
- "backlog backend" / "backlog manager" — **Issue tracker**로 통합.

---

## 핵심 설계 패턴

### 수직 슬라이스 (Vertical Slices)

TDD, to-issues 등에서 일관되게 적용: **수평 슬라이스 금지**, 수직 슬라이스만 허용.

```
WRONG (horizontal): RED → test1,test2,test3,test4,test5 → GREEN → impl1,impl2,...
RIGHT (vertical):   RED→GREEN → test1→impl1 → test2→impl2 → ...
```

### 삭제 테스트 (Deletion Test)

improve-codebase-architecture의 핵심 휴리스틱: 모듈을 삭제했을 때 복잡성이 사라지면 pass-through, 복잡성이 N개 호출자에 재분산되면 가치 있는 모듈.

### 상태 머신 트리아지

triage 스킬의 이슈 흐름: `needs-triage` → `needs-info` | `ready-for-agent` | `ready-for-human` | `wontfix`

---

## 연결 개념

- [[openspec]] — OpenSpec도 Spec-first 협업 패턴. Matt의 스킬은 더 가볍고 조합 가능한 접근
- [[subagent-driven-development]] — Superpowers의 멀티 에이전트 병렬 구현 패턴
- [[엔지니어링-사고-초보자]] — 초보자도 실제 엔지니어링 사고를 갖추도록 하는 방법론
- [[기억하는-개발]] — 세션·컨텍스트·지식을 축적하며 발전하는 AI 협업. Matt의 CONTEXT.md가 이 패턴의 한 구현체
- [[spec-driven-development]] — Spec-Driven Development 전반. Matt은 spec보다 design에 무게

---

## 강점 / 약점

**강점:**
- 작고 조합 가능 — 필요한 스킬만 골라서 사용
- 실용주의 — 수십 년 엔지니어링 경험에 기반
- 모델 agnostic — Claude, Codex, GPT 등 어떤 모델과도 작동
- 공유 언어(shared language) — 단순한 아이디어지만 반복적인 가치를 제공
- 수직 슬라이스 강제 — 수평 슬라이스 안티패턴을 명시적으로 금지

**약점:**
- AGENTS.md / CLAUDE.md 기반 — Hermes와 같은 다른 에이전트 플랫폼에는 직접 호환되지 않음 (skill_manage로 변환 필요)
- 설정 오버헤드 — setup-matt-pocock-skills를 매 레포마다 실행해야 함
- 문서 중심 — 자동화보다 인간의 문서화 습관에 의존
- 코드 분석 없음 — Graphify 같은 AST 분석 도구가 없음 (순수 마크다운 기반)