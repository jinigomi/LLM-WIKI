---
title: Agent Rules Books
created: 2026-05-05
updated: 2026-05-05
type: entity
tags: [ai-coding, coding-agent, prompt-engineering, agent-collaboration, agent-workflow, prompt, tools-infra, knowledge-graph]
sources: [raw/articles/agent-rules-books.md]
analyzed-by: graphify (manual, doc-heavy repo)
---

## 개요

[ciembor/agent-rules-books](https://github.com/ciembor/agent-rules-books)는 **14개 클래식 소프트웨어 공학 서적**에서 추출한 AI 코딩 에이전트용 규칙 모음. Claude Code, Codex, Cursor 등 도구 agnostic한 마크다운 규칙 파일을 MIT 라이선스로 공개.

각 규칙 세트는 **의사결정 규칙(decision rules)**, **트리거 규칙(trigger rules)**, **최종 체크리스트(final checklist)** 구조로 구성.

```
GitHub: ciembor/agent-rules-books
License: MIT | Files: 197 .md (1.7MB) | Version: v0.5
```

## Repository 구조

```
agent-rules-books/
├── README.md / USAGE.md / COMPATIBILITY.md / CRITICISM.md / CHANGELOG.md
├── <book>/              (14개 × full/mini/nano = 42 files)
├── _rule-workbench/     (14개 × full/mini/nano/traceability = 56 files)
└── _compatibility/      (91개 서적 쌍 호환성 분석 파일)
```

### 아키텍처 다이어그램

```
root: README — 진입점 + 릴리스 매트릭스
        │
        ├── USAGE.md        — delivery pattern 가이드
        ├── COMPATIBILITY.md — 14×14 호환성 매트릭스
        ├── CRITICISM.md     — Reddit 비판 + 현재 상태
        └── CHANGELOG.md     — v0.1→v0.5 히스토리
        │
        ▼
BOOKS (14권 × 3 tier)
  ├── 원본 규칙 생성 ──symlink──► _rule-workbench/<book>/full.md
  │                              ├── mini.md  (M* 추적)
  │                              ├── nano.md  (N* 추적)
  │                              └── traceability.md
  │
  └── _compatibility/          ← BOOKS × BOOKS = N² 교차 분석
        91개 서적 쌍 호환성 파일
```

### God Nodes (프로젝트 관통 메타파일)

| 파일 | 크기 | 역할 |
|------|-----:|------|
| `README.md` | 14KB | 진입점 + 릴리스 매트릭스 테이블 |
| `_rule-workbench/PROCESS.md` | 11KB | 규칙 압축 공정 — 전체 품질 기준 |
| `_compatibility/CHECK_COMPATIBILITY.md` | 25KB | 호환성 판단 기준 |

### 커뮤니티 구조 (4개)

| 커뮤니티 | 크기 | 역할 |
|---------|-----:|------|
| **META** | 55KB | 프로젝트 운영 문서 |
| **BOOKS** | 470KB | 규칙 원천 및 파생본 |
| **WORKBENCH** | 647KB | 규칙 생성 공정 + 추적 (품질 보증) |
| **COMPAT** | 586KB | 서적 간 호환성 분석 |

**WORKBENCH ↔ BOOKS:** 1:1 파생 관계 (symlink 기반)
**크로스 브릿지:** `PROCESS.md` → 모든 workbench/*.md

## 3단계 릴리스

| 계층 | 총 라인 | 총 크기 | 평균/권 | 압축률 |
|------|--------:|--------:|--------:|-------:|
| **full** | 6,290줄 | 270KB | ~449줄 | baseline |
| **mini** | 758줄 | 85KB | ~54줄 | **12.1%** (라인) |
| **nano** | ~330줄 | ~30KB | ~24줄 | 31.4% (크기) |

**가장 큰 서적:** Refactoring Guru (full 61KB, 766줄 → nano 2.5KB)
**가장 작은 서적:** The Pragmatic Programmer (nano 2.2KB, ~17줄)

### 규칙 파일 구조 (모든 권에서 동일)

```markdown
# OBEY {book title} by {author}

## When to use
## Primary bias to correct
## Decision rules    (10-40개)
## Trigger rules     (특정 패턴 감지 시)
## Final checklist   (완료前 확인)
```

## 포함된 서적 (14권)

### 설계·아키텍처
- **A Philosophy of Software Design** — 복잡성 관리, 모듈 설계
- **Clean Architecture** — 계층 경계, 의존성 방향
- **Designing Data-Intensive Applications** — 분산 시스템, 일관성
- **Domain-Driven Design** — Aggregate, Bounded Context (61KB로 최대)
- **Domain-Driven Design Distilled** — 전략적 DDD 간추림
- **Implementing DDD** — DDD 구현 패턴
- **Patterns of Enterprise Application Architecture** — Fowler 패턴
- **The Pragmatic Programmer** — 실용주의 결정 휴리스틱

### 코드 품질·리팩토링
- **Clean Code** — 가독성, 함수 설계,命名
- **Code Complete** — 방어적 프로그래밍, 구현 품질
- **Refactoring** — Martin Fowler 리팩토링
- **Refactoring Guru** — refactoring.guru 패턴 (766줄로 최다 규칙)

### 레거시·운영
- **Working Effectively with Legacy Code** — seam 확보, 격리 변경
- **Release It!** — 복원력, 운영 준비성

## 호환성 매트릭스 요약

91개 서적 쌍 분석 결과:

| verdict | 쌍 수 | 설명 |
|---------|------:|------|
| ✅ Complementary | 78 | 함께 로드 가능, 다른 레벨 보강 |
| 🔁 Overlap | 11 | 겹침 — 하나만 선택 |
| ❌ Conflicting | 2 | 동시에 로드 불가 |

**❌ 충돌:**
- `Domain-Driven Design` ↔ `Patterns of Enterprise Application Architecture` — rich model vs. transaction script
- `Implementing DDD` ↔ `PoEAA` — 같은 충돌의 구현 차원

**🔁 주요 겹침:** APoSD↔Clean Code, Clean Code↔Code Complete, Clean Code↔PragProg

## 규칙 압축 공정 (_rule-workbench/PROCESS.md)

14권 모두에 적용되는 표준 공정:

```
원본 서적 → full.md (canonical, symlink)
              ↓
     PROCESS.md 기준 분류:
     - default / book-thesis / decision-changing
     - micro-decision / conflict-resolver / trigger
     - checklist-only / framing
              ↓
     mini.md 생성 (M* ID 추적)
     nano.md 생성 (N* ID 추적)
     traceability.md 기록
```

**핵심 원칙:** 의사결정 동등(decision-equivalent) — 문장 동등이 아닌 핵심 압축
**추적 의무:** 모든 미보존 규칙 = `intentionally lost` 또는 M*/N* ID 명시
**프로세스 우선:** 책별 수정이 아닌 PROCESS.md 수정이 먼저

## 비판과 현재 상태

| 비판 | 유효성 | 현재 상태 |
|------|-------:|---------|
| 개선 측정 기준 부재 | 9/10 | 약하게 대응 (릴리스 메트릭스 추가, 실전 측정X) |
| 토큰 소모·컨텍스트 오염 | 9/10 | 대부분 해결 (3-tier로 해결) |

## 연결 개념

- [[agentic-ai-engineering]] — AI 에이전트 행동 예측/통제. 이 규칙은 [[harness-engineering]]의 일종
- [[spec-driven-development]] — 규칙을 먼저 선언하는 개발. 이 리포는 서적 기반 규칙을 명시적으로 선언
- [[prompt-master]] — AI 도구별 프롬프트 최적화 스킬
- **karpathy-guidelines** — 코딩 규칙 스킬. agent-rules-books는 더 체계적이고 도구 agnostic
- [[subagent-driven-development]] — 멀티 에이전트 구현. 규칙 기반 품질 게이트 역할
- [[matt-pocock-tdd]] — TDD 강제. WELC 규칙도 유사하게 테스트 강제
- [[high-performance-git]] — Wiki에 이미 정리된 원본 서적 요약