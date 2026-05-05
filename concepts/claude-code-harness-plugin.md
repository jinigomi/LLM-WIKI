---
title: Claude Code 하네스 플러그인
type: concept
tags: [harness, claude-code, plugin, subagent, skill, hook, worktree]
created: 2026-05-04
source: "wikidocs.net/book/19712 - Part 10. Claude Code 하네스 플러그인"
parent: "[[harness-engineering|하네스 엔지니어링]]"
---

# Claude Code 하네스 플러그인

**Parts 1~8의 추상 위에 Claude Code의 4 프리미티브를 얹어 작동하는 구현체.** GitHub `harness-coding-plugin` 저장소로 배포된다.

## 두 레벨의 이중 집행

하네스의 원칙 중 일부는 프롬프트만으로는 강제할 수 없고, 도구 수준의 집행이 필요하다:

| 문제 | 근본 원인 | 집행 레벨 |
|------|----------|----------|
| 자기 평가 편향 | 모델 능력 문제 | 프롬프트 레벨 (모델 개선) |
| 경계 침범 | 도구 능력 문제 | 도구 레벨 (Hook으로 강제) |

## 4 프리미티브

| 프리미티브 | 하네스에서의 역할 |
|-----------|-----------------|
| **Subagent** | 격리된 컨텍스트로 Planner·Generator·Evaluator 실행 |
| **Skill** | A군: 워크플로 진입점 (`init-project`, `start-feature`, `run-sprint`, `run-evaluation` 등) + B군: 역할 규율 (Planner/Generator/Evaluator 각각의 행동 강령) |
| **Hook** | PreToolUse로 매개체 오너십 강제 — Planner가 validation.md에 쓰려 하면 차단 |
| **Worktree** | 변경 단위 격리 — 각 Feature마다 별도 worktree |

## 디렉토리 레이아웃

```
harness-coding-plugin/
├── .claude-plugin/
│   └── plugin.json               # 매니페스트
├── agents/                       # 격리된 서브에이전트 정의
│   ├── planner.md
│   ├── generator.md
│   └── evaluator.md
├── skills/
│   ├── init-project/SKILL.md     # A군: 워크플로 진입점
│   ├── interview-constitution/SKILL.md
│   ├── start-feature/SKILL.md
│   ├── negotiate-contract/SKILL.md
│   ├── run-sprint/SKILL.md
│   ├── run-evaluation/SKILL.md
│   ├── diagnose-harness/SKILL.md
│   ├── recover-boundary/SKILL.md  # 회복 스킬
│   ├── ...                        # + 각 역할별 규율 스킬 (B군)
├── hooks/
│   ├── pre-tool-use-mediums.py   # 매개체 오너십 강제
│   └── ...
└── templates/                    # 매개체 파일 템플릿
    ├── mission.md
    ├── tech-stack.md
    ├── plan.md
    ├── validation.md
    └── ...
```

## 서브에이전트 (agents/)

- **planner.md** — Constitution 작성, Feature Spec 작성. plan.md를 쓰고 구현 방법은 절대 쓰지 않는다
- **generator.md** — 코드 작성 → 테스트 → 빌드 → CI → 배포 → 헬스체크 6단계 완료 체인
- **evaluator.md** — validation.md 작성, Layer 1~3 검증. plan.md를 절대 읽지 않는다

## Skill 체계

### A군 — 워크플로 진입점 (사용자 명시 호출)

- `init-project` — 프로젝트 초기화, Constitution 인터뷰
- `start-feature` — Feature Spec 작성 시작
- `negotiate-contract` — Sprint Contract 협상
- `run-sprint` — Generator 실행
- `run-evaluation` — Evaluator 실행
- `diagnose-harness` — 실패 진단

### B군 — 역할 규율 (워크플로 내에서 자동 로드)

- Planner·Generator·Evaluator 각각의 행동 강령
- "절대 하지 말아야 할 것" 목록 + 위반 시 복구 절차

## 도구 종속성과 이식성

| 자산 유형 | 도구 독립성 | Claude Code 이식 시 손실 |
|----------|-----------|------------------------|
| **콘텐츠** (역할 정의, 시나리오, 회복 절차) | 도구 무관 ✅ | 없음 |
| **집행 메커니즘** (자동 발동, 서브에이전트 preload, Hook) | Claude Code 종속 ⚠️ | Codex CLI·Gemini CLI 이식 시 Hook 기능 손실 |

이식 매핑(10.8절)에서 각 도구별 손실되는 기능을 명시적으로 제시한다.

## 디자인 원칙

> "한 도구로 내려가되 한 도구에 갇히지 않는다."

- 본문은 **왜 이렇게 만들었는가**를 설명
- GitHub 저장소는 **그대로 가져다 쓸 수 있는 코드**를 제공
- Hook이 없는 환경에서는 B군 Skill의 "절대 하지 말아야 할 것" 목록을 프롬프트 수준으로 대체

## 관련 페이지

- [[harness-engineering|하네스 엔지니어링]]
- [[planner-role|Planner 역할]]
- [[generator-role|Generator 역할]]
- [[evaluator-role|Evaluator 역할]]
- [[mediums-framework|매개체 체계]]
- [[failure-modes|실패 모드와 회복]]