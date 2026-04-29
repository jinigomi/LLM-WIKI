---
title: OpenSpec
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [AI, LLM, agent-workflow, spec-driven, open-source, CLI, tool, software-development]
sources: [https://github.com/Fission-AI/OpenSpec]
confidence: high
---

# OpenSpec

## What It Is
**AI-native spec-driven development 프레임워크.** 인간과 AI가 코드를 작성하기 전에 스펙(요구사항)에 합의하는 경량 명세 계층을 제공한다.

- **개발 주체**: Fission AI
- **npm 패키지**: `@fission-ai/openspec` (v1.3.1)
- **라이선스**: MIT
- **요구사항**: Node.js ≥ 20.19.0
- **핵심 의존성**: Commander, Zod, Chalk, @inquirer/prompts, fast-glob, PostHog
- **별표**: 900+

## 핵심 철학
```
fluid not rigid         — 페이즈 게이트 없음, 필요한 걸 필요한 순서에
iterative not waterfall — 만들면서 배우고, 배우면서 개선
easy not complex        — 최소한의 세팅, 최소한의 세레모니
brownfield-first        — 기존 코드베이스에서도 작동, greenfield만을 위한 게 아님
```

## 아키텍처 개요

### Specs → Changes 구조
```
openspec/
├── specs/              ← Source of truth (현재 시스템 동작 명세)
│   └── {domain}/spec.md
├── changes/            ← Proposed modifications (제안된 변경)
│   └── {change-name}/
│       ├── proposal.md
│       ├── specs/       ← Delta specs (ADDED/MODIFIED/REMOVED)
│       ├── design.md
│       └── tasks.md
├── config.yaml
└── schemas/
```

**Specs**: 시스템이 "현재 어떻게 동작하는지"를 기술하는 소스 오브 트루스.
**Changes**: 각 변경사항을 폴더로 패키징. 프로포절·디자인·태스크·델타 스펙 포함.

### Delta Specs
브라운필드 개발의 핵심 — 변경사항만 기술:
```markdown
## ADDED Requirements    → 아카이브 시 메인 스펙에 추가
## MODIFIED Requirements → 기존 요구사항 대체
## REMOVED Requirements  → 메인 스펙에서 삭제
```

### OPSX 워크플로우 (v1.3+)
레거시 `openspec:{proposal|apply|archive}` 대신 새로운 아티팩트 기반 워크플로우:
- `/opsx:propose` — 제안 + 기획 아티팩트 한 번에
- `/opsx:explore` — 아이디어 탐색 (비정형)
- `/opsx:apply` — 태스크 구현
- `/opsx:archive` — 완료 시 아카이브
- 확장 명령어: `new`, `continue`, `ff`, `verify`, `sync`, `bulk-archive`, `onboard`

### Schema-Driven 아키텍처
YAML 스키마로 아티팩트와 그 의존성을 정의:
```yaml
artifacts:
  - id: proposal
    generates: proposal.md
    requires: []
  - id: specs
    generates: specs/**/*.md
    requires: [proposal]
  - id: design
    generates: design.md
    requires: [proposal]
  - id: tasks
    generates: tasks.md
    requires: [specs, design]
```

### 25+ AI 도구 지원
각 도구별 어댑터로 슬래시 커맨드 자동 생성 — Claude Code, Cursor, Windsurf, Cline, Codex, Copilot, Amazon Q, CodeBuddy, Gemini 등

## 기술 스택
- **Language**: TypeScript (ESM, `"type": "module"`)
- **CLI**: Commander.js
- **Validation**: Zod v4
- **Config**: YAML
- **Shell Completion**: Bash, Zsh, Fish, PowerShell
- **Testing**: Vitest
- **Build**: Node.js `tsc` + custom `build.js`
- **Versioning**: Changesets

## 코드 구조 (135개 소스 파일)
```
src/
├── cli/                    # CLI 진입점
├── commands/               # 커맨드 구현체 (init, validate, change, config, ...)
│   └── workflow/           # OPSX 워크플로우 커맨드
├── core/
│   ├── artifact-graph/     # 아티팩트 의존성 그래프 엔진
│   ├── command-generation/ # 25+ 도구 어댑터 + 커맨드 생성
│   │   └── adapters/       # claude, cursor, windsurf, cline, codex, ...
│   ├── completions/        # Shell completion (Bash/Zsh/Fish/PowerShell)
│   ├── config/             # 설정 관리
│   ├── parsers/            # Markdown 파서, Change 파서
│   ├── templates/          # 워크플로우별 템플릿
│   │   └── workflows/      # propose, apply, archive, onboard, explore, ...
│   ├── validation/         # 스펙 유효성 검증
│   ├── profiles.ts         # 프로필/워크플로우 설정
│   ├── global-config.ts    # 글로벌 설정
│   ├── migration.ts        # 레거시 → 신규 마이그레이션
│   └── init.ts             # 초기화 로직
├── utils/                  # 유틸리티
├── telemetry/              # PostHog 익명 사용 통계
├── ui/                     # TUI (Welcome screen, ASCII patterns)
└── prompts/                # Inquirer 기반 인터랙티브 프롬프트
```

## Graphify 분석 결과
- 212개 파일, 774개 AST 노드, 1648개 엣지, 36개 커뮤니티
- **God Node**: `log()` — 49개 연결, 최대 betweenness centrality (0.216), 거의 모든 커뮤니티 연결
- **주요 허브**: InitCommand, ZshInstaller, Validator, getSkillTemplates(), FileSystemUtils, UpdateCommand, MarkdownParser
- **주요 커뮤니티**:
  - Community 0: Artifact Graph + Change 생성 (54 노드)
  - Community 1: Skill/Command 템플릿 생성 (36 노드)
  - Community 2: Archive/List/FileSystem (31 노드)
  - Community 3: Shell Installer (6 노드, 높은 cohesion)
  - Community 4: Profile/Config 관리 (28 노드)
  - Community 8: Init + FileSystem 유틸 (24 노드)

## 유사 도구와 비교
- **vs Spec Kit** (GitHub): Spec Kit은 더 무겁고 (Python 필요, rigid phase gate). OpenSpec은 가볍고 유연.
- **vs Kiro** (AWS): Kiro는 IDE 락인 + Claude 전용. OpenSpec은 모든 도구에서 작동.

## 기타
- **Telemetry**: PostHog로 익명 명령어 사용 통계 수집. `OPENSPEC_TELEMETRY=0` 비활성화. CI에서는 자동 비활성화.
- **Discord**: https://discord.gg/YctCnvvshC
- **창립자 X**: @0xTab