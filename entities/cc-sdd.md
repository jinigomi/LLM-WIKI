---
title: cc-sdd (Kiro-style Agentic SDLC)
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [spec-driven-development, agent-framework, claude-code, codex, cursor, sdlc, kiro]
sources: [https://github.com/gotalab/cc-sdd]
confidence: high
---

## 개요

cc-sdd는 **Kiro IDE 스타일의 Spec-Driven Development** 를 8개 AI 코딩 에이전트에서 동일한 17개 스킬로 구현한 프레임워크다. "Boundary-first" 설계를 강조하며, 스펙을 시스템 부분 간의 **계약(contract)** 으로 취급한다. 코드는 진실의 원천이고, 스펙은 경계를 명시적으로 만든다.

- **저자:** @gotalab
- **라이선스:** MIT
- **언어:** TypeScript (CLI 도구), Markdown (스킬)
- **지원 에이전트:** Claude Code, Codex (Stable) + Cursor, Copilot, Windsurf, OpenCode, Gemini CLI, Antigravity (Beta)
- **Graphify:** 214 노드 · 340 엣지 · 44 커뮤니티

## 핵심 설계 철학

> "Boundaries are not overhead. They are what lets you move freely inside while protecting the outside."

cc-sdd는 스펙을 "AI에게 주는 명령서"가 아니라 **시스템 간 계약** 으로 본다. 명시적 경계가 있어야 사람과 에이전트가 동기화 없이도 병렬로 작업할 수 있다는 게 핵심 가설.

## 아키텍처 레이어

### Discovery → Specification → Implementation

```text
/kiro-discovery "아이디어"
    ↓ brief.md + roadmap.md
/kiro-spec-init → /kiro-spec-requirements → /kiro-spec-design → /kiro-spec-tasks
    ↓
/kiro-impl (자율 구현: TDD + 독립 리뷰 + auto-debug)
```

### Graphify 커뮤니티 구조

| 커뮤니티 | Cohesion | 설명 |
|----------|----------|------|
| Community 0 | 0.12 | 포매팅/출력 — 24 노드 (가장 큰 클러스터) |
| Community 1 | 0.15 | CLI 인자 파싱 + 설정 병합 |
| Community 2 | 0.24 | 파일 작업 + 경로 검증 (`buildFileOperations`, `assertPathInsideRoot`) |
| Community 3 | 0.23 | 매니페스트 로딩 + 플랜 생성 |
| Community 4 | 0.22 | 템플릿 렌더링 + 컨텍스트 빌딩 |
| Community 5 | 0.36 | 파일 실행 + 충돌 해결 (`executeProcessedArtifacts`, `resolveAction`) |

**God Nodes:** `runPlanExecution`(15 엣지), `buildFileOperations`(14 엣지), `handleAppend`(10 엣지)

### CLI 구조

```text
tools/cc-sdd/src/
├── index.ts       # 진입점: runCli() → handleDryRun / runPlanExecution
├── cli/
│   ├── args.ts    # CLI 인자 파싱
│   └── policies.ts # 카테고리 정책 결정
├── plan/
│   ├── executor.ts     # 실행 엔진 (resolveAction)
│   ├── fileOperations.ts # 파일 CRUD
│   └── printer.ts      # 드라이런 출력
├── manifest/
│   ├── loader.ts    # .kiro/ 디렉토리 로딩
│   └── planner.ts   # planFromFile → planFromManifest
├── template/
│   ├── context.ts   # 템플릿 컨텍스트 빌딩
│   └── renderer.ts  # JSON 템플릿 렌더링
└── config/
    └── store.ts     # 설정 관리
```

## v3.0 주요 기능

### 1. `/kiro-discovery`
새로운 진입점. 아이디어를 분석해 작업 경로를 자동 결정:
- 단일 스펙 확장 / 구현만 / 새 스펙 1개 / 멀티 스펙 분할 / 혼합

### 2. `/kiro-impl` 자율 구현
- 각 태스크마다 **신선한 구현자** (TDD: RED→GREEN)
- **독립적 리뷰어** 서브에이전트
- **auto-debug** — 실패 시 루트 원인 분석
- 이전 태스크의 학습이 `tasks.md`의 `## Implementation Notes`로 전파

### 3. Boundary-first 스펙 규율
- `design.md`에 **File Structure Plan** 포함
- 태스크에 `_Boundary:_` + `_Depends:_` 어노테이션
- 리뷰/검증 시 경계 침범 감지

### 4. `/kiro-spec-batch` 멀티 스펙
- roadmap.md에서 여러 스펙을 병렬 생성
- 크로스 스펙 리뷰로 모순/중복/인터페이스 불일치 감지

## 강점

- **8개 에이전트에서 동일한 17개 스킬셋** — 에이전트 종속성 없음
- **경계 중심 설계** — 파일 구조 계획이 태스크 경계를 만듦
- **자율적 구현 + 독립 리뷰** — 작업자/리뷰어/디버거 역할 분담
- **13개 언어 지원** — en, ja, zh-TW, zh, es, pt, de, fr, ru, it, ko, ar, el
- **npx 한 줄 설치** — `npx cc-sdd@latest`
- **외부 의존성 없음** — 서브에이전트는 각 플랫폼의 네이티브 프리미티브로 스폰

## 약점

- **많은 커뮤니티가 비어 있음** (Communities 6-43, 0 노드) — 노이즈 제거 필요
- **Stable은 Claude Code + Codex만** — Cursor/Copilot/Windsurf 등은 Beta
- **17개 스킬 학습 곡선** — 스킬 모드의 progressive disclosure가 있지만 초기 진입은 복잡
- **레거시 커맨드 모드 중복** — `/kiro:*` 레거시 + `--claude-skills` 등 마이그레이션 경로가 복잡

## 연결 개념

- [[spec-driven-development]] — SDD 방법론의 기본 개념. cc-sdd는 Kiro 스타일 구현체
- [[don-cheli-sdd]] — 같은 SDD 도메인의 경쟁 프레임워크. Docker 런타임 + TDD 강제
- [[harness-engineering]] — AI 에이전트 행동 제어. cc-sdd의 경계/스펙은 harness 규칙의 한 형태
- [[subagent-driven-development]] — `/kiro-impl`의 구현자/리뷰어 패턴과 연결
- mihomo-rust-claude-code-porting ⚠️ 페이지 미생성 — 40개 spec 기반 Go→Rust 포팅 사례. cc-sdd와 유사한 spec 계층 구조