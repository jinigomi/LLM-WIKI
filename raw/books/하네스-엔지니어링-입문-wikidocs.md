---
source_url: https://wikidocs.net/book/19559
ingested: 2026-04-27
type: book-summary
tags: [AI, LLM, agent, engineering, software-development]
---

# 하네스 엔지니어링 입문

> 위키독스 전자도서 | https://wikidocs.net/book/19559
> 스크래핑 일시: 2026-04-27

---

## 0. 들어가며

### 0.1 이 책을 쓴 이유

AI 에이전트가 코드를 작성하고, 테스트를 돌리고, PR을 올리는 시대가 되었습니다.
하지만 자유로운 에이전트는 예측 불가능한 실수를 저지릅니다.
하네스 엔지니어링은 이 문제를 구조적으로 해결합니다.

**핵심 자료 3가지:**
1. Mitchell Hashimoto — Cursor/Roo/Forge 개발자, 6단계 도입 여정
2. OpenAI Harness Engineering — Codex 에이전트 가이드
3. Martin Fowler — 피드백 루프 분류 (Pre-flow / Post-flow)

---

## 1. 하네스 엔지니어링 등장 배경

### 1.1 프롬프트, 컨텍스트, 하네스 엔지니어링

AI 에이전트 개발에서 가장 중요한 세 가지:
- **프롬프트**: 에이전트에게 보내는 지시
- **컨텍스트**: 에이전트가 판단하기 위해 참고하는 정보
- **하네스**: 에이전트의 행동을 구조적으로 통제하는 장치

### 1.2 에이전트와 하네스 엔지니어링

에이전트가 자율적으로 작업할수록 암묵적 규칙은 사라집니다.
하네스는 이 공백을 메우는 구조적 장치입니다.

---

## 2. 하네스 엔지니어링 개념

### 2.1 하네스 엔지니어링이란?

에이전트의 행동을 예측 가능하고 통제 가능하게 만드는 엔지니어링 분야.
"실수를 고치는 것은 1회성 해결에서 구조를 바꿔 지속적으로 해결할 수 있게 하는 것."

### 2.2 Hashimoto의 6단계 도입 여정

| 단계 | 핵심 변화 | 새로운 병목 |
|------|-----------|-------------|
| 1단계: 자동완성 | AI가 제안, 사람이 판단 | 프롬프트 작성 능력 |
| 2단계: 코드 생성 | AI가 코드 작성 | 프롬프트 작성 능력 |
| 3단계: 인터랙티브 편집 | 맥락을 가진 대화 | 컨텍스트 준비 비용 |
| 4단계: 단일 에이전트 | AI가 스스로 실행 | 에이전트 방향 설정 |
| 5단계: 병렬 에이전트 | 여러 에이전트 동시 작업 | 일관성 관리 |
| 6단계: 자율 에이전트 시스템 | 전 과정 자율 수행 | 재발 방지 구조 |

**4단계 이상에서 하네스 없이 에이전트를 운용하는 것은 안전망 없이 줄타기를 하는 것과 같습니다.**

### 2.3 하네스의 4가지 구성요소란?

```
프로젝트 루트/
├── AGENTS.md         ← ① 지시 문서
├── .eslintrc         ← ② 아키텍처 제약
├── tests/            ← ③ 피드백 루프
└── docs/             ← ④ 지식 저장소
    ├── decisions/
    └── conventions/
```

1. **지시 문서** (AGENTS.md / CLAUDE.md) — 에이전트가 "어떻게 할지" 안다
2. **아키텍처 제약** — 잘못된 방향으로 가면 자동으로 막힌다
3. **피드백 루프** — 결과가 맞는지 즉시 확인한다
4. **지식 저장소** — 왜 이렇게 하는지 이유를 안다

---

## 3. 하네스 구성요소

### 3.1 지시문서

에이전트가 작업을 시작하기 전에 읽는 행동 지침.
파일명: `AGENTS.md` (OpenAI Codex용) 또는 `CLAUDE.md` (Anthropic Claude용)

**좋은 지시 문서의 조건:**
- 구체적인 규칙 (추상적이지 않음)
- 절대 금지 항목 명시
- 테스트/커밋 규칙 포함

**기본 템플릿:**
```markdown
# AGENTS.md

## 프로젝트 개요
- 목적: 사용자 인증 및 결제를 처리하는 백엔드 API
- 언어: Python 3.11
- 프레임워크: FastAPI

## 디렉토리 구조
src/
├── api/ # 라우터만. 비즈니스 로직 금지
├── services/ # 비즈니스 로직
├── models/ # DB 모델
└── utils/ # 순수 함수만

## 코드 규칙
- 함수명: snake_case
- 클래스명: PascalCase
- 한 함수는 하나의 역할만 수행한다
- 타입 힌트 필수

## 절대 금지
- `src/core/` 파일 수정 금지 (레거시, 건드리면 장애 발생)
- `print()` 사용 금지 → `logger.info()` 사용
- 직접 DB 쿼리 금지 → 반드시 서비스 레이어 경유

## PR 규칙
- 커밋 메시지: `feat:`, `fix:`, `refactor:` 접두사 사용
- PR 하나에 하나의 변경만
- 테스트 없는 PR은 올리지 않는다

## 테스트
- 테스트 파일 위치: `tests/`
- 실행 명령: `pytest tests/`
- 새 기능에는 반드시 테스트 추가
```

**디렉토리별 지시 문서:**
프로젝트가 커지면 루트의 AGENTS.md 하나로는 부족합니다. 각 디렉토리에 별도 파일을 둘 수 있습니다.

```markdown
# src/api/AGENTS.md
## 이 디렉토리의 역할
라우터 정의만 담당합니다.
## 규칙
- 비즈니스 로직은 services/로 위임할 것
- 응답 형식은 항상 `ResponseModel`을 사용할 것
- 인증이 필요한 엔드포인트는 `@require_auth` 데코레이터 필수
```

**시작 팁:** 완벽한 문서를 처음부터 만들려 하지 마세요. 에이전트가 실수할 때마다 해당 규칙을 추가하는 방식으로 점진적으로 완성해 나가는 것이 가장 효과적입니다.

### 3.2 아키텍처 제약

지시 문서가 규칙을 **알려주었다면**, 아키텍처 제약은 규칙을 **강제**합니다.

**린터 설정 (Python — ruff):**
```toml
# pyproject.toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N"]

# 절대 import만 허용 (상대 import 금지)
[tool.ruff.lint.flake8-tidy-imports]
ban-relative-imports = "all"
```

**레이어 간 import 제약 (Python — import-linter):**
```toml
# .importlinter
[importlinter]
root_package = src

[importlinter:contract:레이어 규칙]
name = 레이어 간 의존성 규칙
type = layers
layers = src.api, src.services, src.models
# api → services → models 방향만 허용
# models에서 api를 참조하면 자동으로 오류 발생
```

**린터 설정 (JavaScript/TypeScript — ESLint):**
```json
// .eslintrc.json
{
  "rules": {
    "no-console": "error",
    "no-unused-vars": "error",
    "prefer-const": "error"
  },
  "settings": {
    "import/no-restricted-paths": [{
      "zones": [{
        "target": "./src/api",
        "from": "./src/models",
        "message": "api 레이어에서 models 직접 참조 금지. services를 경유하세요."
      }]
    }]
  }
}
```

**pre-commit 훅 — 저장소에 들어오기 전에 차단:**
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.0
    hooks:
      - id: ruff
      - id: ruff-format
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.9.0
    hooks:
      - id: mypy
```

**원칙:** 규칙은 문서보다 코드로 강제할 때 더 잘 지켜진다.

### 3.3 피드백 루프

에이전트 행동을 실시간으로 교정하는 장치. Martin Fowler는 이를 두 가지로 분류:

- **Pre-flow Feedback**: 에이전트가 작업을 수행하기 **전**에 검사 (린트, 타입 체크)
- **Post-flow Feedback**: 에이전트가 작업을 완료한 **후**에 검사 (테스트, 리뷰)

피드백이 빠를수록 에이전트는 더 적은 비용으로 올바른 결과를 냅니다.

### 3.4 지식 저장소

사람의 결정과 맥락을 축적하는 디렉토리.

```
docs/
├── decisions/    # 왜 이 결정을 내렸는가
└── conventions/  # 채택한 규칙과 그 이유
```

에이전트는 매 세션 백지로 시작합니다. 지식 저장소는 그 공백을 채웁니다.

### 3.5 가비지 컬렉션

(내용 미수집 — wikidocs에서 확인 필요)

---

## 4. 실천구축 (작성중)

## 5. 사례연구 (작성중)

## 부록

---

*스크래핑 완료 — 전체 13개 챕터 중 10개 확보 (3.5, 4, 5는 작성중으로 아직 미수록)*
*원본: [[https://wikidocs.net/book/19559]]*