---
title: 하네스 엔지니어링 입문 (위키독스)
created: 2026-04-27
updated: 2026-04-27
type: entity
source: [https://wikidocs.net/book/19559](https://wikidocs.net/book/19559)
tags: [AI, LLM, agent, engineering, software-development]
---

# 하네스 엔지니어링 입문 — 위키독스 요약

## 개요

AI 에이전트가 자율적으로 코드를 작성하는 시대, 하네스 엔지니어링은 **에이전트의 행동을 예측 가능하고 통제 가능하게** 만드는 소프트웨어 엔지니어링 분야.

## 핵심 개념

### Hashimoto의 6단계 도입 여정
| 단계 | 핵심 변화 | 병목 |
|------|-----------|------|
| 1-2 | AI가 제안/생성, 사람이 판단 | 프롬프트 작성 |
| 3 | 맥락 있는 대화 | 컨텍스트 준비 |
| 4 | AI가 스스로 실행 | 방향 설정 |
| 5-6 | 자율/병렬 처리 | 일관성·재발방지 |

**4단계 이상에서는 하네스가 필수.**

### 하네스의 4가지 구성요소
1. **지시 문서** — `AGENTS.md`/`CLAUDE.md`
2. **아키텍처 제약** — 린터, import-linter, pre-commit
3. **피드백 루프** — Pre-flow / Post-flow
4. **지식 저장소** — `docs/decisions/`, `docs/conventions/`

## 도구 스택 (이 책에서 언급)

| 용도 | 도구 |
|------|------|
| Python 린터 | ruff |
| 타입 체크 | mypy |
| import 검사 | import-linter |
| pre-commit | pre-commit hooks |
| JS 린터 | ESLint |
| import 검사 (JS) | eslint-plugin-import |

## 관련 개념
- [[harness-engineering]] — 핵심 개념 페이지
- mihomo-rust-claude-code-porting ⚠️ 페이지 미생성 — Agent Team 하네스 실전 사례 (3만 라인 Go→Rust 포팅)
- AI-agent — 에이전트 관련
- prompt-engineering — 프롬프트 관련

## 심층 분석 (2026-04-27)

### 두 소스의 보완적 관계
- **위키독스**: Layer 1-4 (Local harness) — 개발자 관점
- **AWS Blog**: Layer 5-6 (Production harness) — 인프라 관점

### 4가지 핵심 원칙
1. **통제 불가능한 것은 격리하라** — linter/Fargate로 영향 범위 한정
2. **실패는 구조로 해결하라** — 1회성 수리가 아닌 구조적 개선
3. **하네스도 검증 가능해야 한다** — Validator/Tracker로 하네스 자체 검증
4. **단계가 올라갈수록 병목이 이동한다** — 1-2: 프롬프트 → 5-6: 인프라

### 미비 영역
- 3.5 가비지 컬렉션 미수집 (内容 확인 필요)
- 4·5장 미완성
- A2A/MCP 프로토콜 미다루
- AGENTS.md ↔ 린터 설정 자동 동기화 방법 미제시

### 종합 평가
**위키독스:** ★★★☆☆ — 실용적 설정 파일 제공, Layer 1-4 중심
**AWS Blog:** ★★★★☆ —实证적 프로덕션 아키텍처, Layer 5-6 중심
**통합:** 두 소스 함께 보면 local+production 전체 스펙트럼 이해 가능

---

*원본: [https://wikidocs.net/book/19559](https://wikidocs.net/book/19559)*
*스크래핑: 2026-04-27*
*보관 위치: /Users/bricoleur/secall/하네스-엔지니어링-입문.md*