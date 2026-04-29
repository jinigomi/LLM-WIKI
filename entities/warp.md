---
title: Warp
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [terminal, agent, rust, open-source, developer-tools, ai-native, tool]
sources: [raw/articles/warp-github-repo-analysis.md]
confidence: high
---

# Warp — Agentic Development Environment

> **한줄:** 터미널에서 탄생한 AI 네이티브 통합 개발 환경. Rust 기반, 자체 UI 프레임워크 WarpUI, 내장 코딩 에이전트, MCP 지원.

- **Website:** [warp.dev](https://www.warp.dev)
- **Repository:** [github.com/warpdotdev/warp](https://github.com/warpdotdev/warp)
- **License:** AGPL v3 (core), MIT (WarpUI framework)
- **Founded Sponsor:** OpenAI — GPT 모델 기반 에이전트 워크플로우
- **Language:** Rust (~3,149개 소스 파일, 52,578개 AST 노드, 975개 그래프 커뮤니티)
- **Platform:** macOS, Windows, Linux + WASM

---

## 아키텍처 개요

### Custom UI Framework — WarpUI

Warp는 자체 UI 프레임워크 **WarpUI**를 Rust로 구현했다:

- **Entity-Component-Handle 패턴:** `App` 객체가 모든 View/Model을 소유 (엔티티). View는 `ViewHandle<T>`로 다른 View를 참조. 직접 소유가 아닌 핸들 기반 간접 참조로 메모리 안전성 확보
- **Flutter-inspired Elements:** 시각적 레이아웃을 Elements로 기술
- **Actions 시스템:** 이벤트 핸들링을 Action 기반으로 처리
- **MouseStateHandle:** 생성 시 한 번만 만들고 복제해서 사용; 렌더링 중 `MouseStateHandle::default()` 인라인 생성 시 마우스 인터랙션 불가 (중요한 함정)

### 크레이트 구조 (63개 크레이트)

**Core 프레임워크:**
| 크레이트 | 역할 |
|----------|------|
| `warpui_core` | UI 코어: App, ViewContext, ModelContext, handle 시스템 |
| `warpui` | warpui_core를 감싼 공개 API |
| `warpui_extras` | 추가 UI 컴포넌트 |
| `warp_core` | 공통 유틸리티, 플랫폼 추상화, FeatureFlag 시스템 |

**Terminal:**
| 크레이트 | 역할 |
|----------|------|
| `warp_terminal` | 터미널 에뮬레이션, 그리드 모델 |
| `editor` (`warp_editor`) | 리치 텍스트 편집, 소프트 랩, 멀티라인 |
| `sum_tree` | Sum Tree 자료구조 (편집기/터미널용) |

**AI / Agent:**
| 크레이트 | 역할 |
|----------|------|
| `ai` | Agent 액션, 태스크, 스킬, 멀티에이전트 API 타입 |
| `computer_use` | GUI 자동화 / 컴퓨터 사용 |
| `agent_sdk` | 외부 에이전트 SDK (Claude Code, Codex, Gemini CLI 등) |
| `mcp` | Model Context Protocol 통합 — Warp/Claude/VS Code 제공자 지원 |

**인프라:**
| 크레이트 | 역할 |
|----------|------|
| `graphql` | GraphQL 클라이언트 + 스키마 생성 |
| `http_server` | 내장 HTTP 서버 (axum 0.8.4) |
| `websocket` | WebSocket 통신 |
| `ipc` | 프로세스 간 통신 |
| `persistence` | Diesel ORM + SQLite |
| `firebase` | Firebase 인증 통합 |
| `warp_server_client` | Warp 클라우드 서버 API 클라이언트 |

**주요 의존성:** Tokio (async runtime), NuShell, Alacritty, Hyper, FontKit

---

## AI Agent 시스템

### Agent Mode

Warp의 핵심 차별점 — 터미널 안에서 직접 AI 에이전트 실행:

- **Conversation 기반:** 대화는 Task → Exchange → Action 계층 구조. Todo 리스트 자동 관리
- **Task Store:** 작업 상태 지속 관리
- **Skill 시스템:** `SkillDescriptor` / `ParsedSkill` — Claude Code의 SKILL.md 패턴과 유사
- **Agent SDK:** `agent_sdk/` 모듈에서 Claude Code, Codex, Gemini CLI 등 외부 CLI 에이전트 통합 (36개 파일, driver 서브시스템)
- **Ambient Agents:** 백그라운드에서 지속 실행되는 에이전트 (`ambient_agents/`)
- **Blocklist:** AI 응답을 구조화된 블록(텍스트, 코드, diff, 아티팩트)으로 렌더링

### MCP 통합

`app/src/ai/mcp/` — 풀 MCP 클라이언트:
- Warp, Claude, VS Code 등 여러 MCP Provider 지원
- File-based + 템플릿 기반 MCP 서버 매니저
- 환경변수 관리, 설정 파일 감시
- Warp Drive를 통한 MCP 서버 설정 동기화

### 실행 프로필 (Execution Profiles)

`execution_profiles/` — 에이전트 실행 환경 설정을 프로필로 관리. 클라우드 환경(`cloud_environments/`)과 연동.

### Facts / Skills

- **Facts:** `facts/` — 지속적 메모리 시스템. [[llm-wiki]]의 raw/articles 개념과 유사
- **Skills:** `skills/` — Claude Code 스타일의 스킬 정의 + 파싱

---

## Warp Drive (클라우드 동기화)

`app/src/drive/` — 협업/동기화 레이어:

- **동기화 대상:** Workflow, Agent Workflow, AI Facts, Notebooks, Env Vars, MCP Servers
- **폴더/공유:** `folders/`, `sharing/`로 협업 지원
- **Cloud Object:** `CloudObject` 추상화 위에 GenericStringModel, JsonModel
- **Diesel ORM + SQLite** 로컬 지속성, 서버와 GraphQL/WebSocket 동기화

---

## 설계 원칙

### Feature Flag 시스템

```rust
// warp_core/src/features.rs
pub enum FeatureFlag { YourNewFeature, ... }
pub const DOGFOOD_FLAGS: &[FeatureFlag] = &[...];
pub const PREVIEW_FLAGS: &[FeatureFlag] = &[...];

// 사용
if FeatureFlag::YourNewFeature.is_enabled() { ... }
```

- **컴파일타임이 아닌 런타임 체크 선호** — `#[cfg]` 대신 `is_enabled()`
- 플래그는 제품 수준에서 고수준으로; 출시 안정화 후 제거

### 터미널 모델 락 주의

`TerminalModel`의 `model.lock()`은 데드락 위험 — 이미 락이 잡힌 콜스택에서 중복 락 획득 시 UI 프리즈. 짧은 스코프 + 하향 전달로 대응.

### Exhaustive Matching

와일드카드 `_` 대신 모든 variant 명시적 매칭 — 새 enum variant 추가 시 컴파일러가 누락 감지.

---

## 관련 Wiki 페이지

- [[superpowers]] — 유사한 AI 에이전트 방법론 플러그인 (Anthropic)
- [[subagent-driven-development]] — Superpowers의 Two-Stage Review 패턴
- [[hermes-vs-boop]] — 다른 에이전트 개발 환경 비교 프레임워크
- [[mcp-프로토콜]] — MCP의 상세 프로토콜 설명
- [[오케스트레이션]] — AI 에이전트 간 작업 조율

---

## 핵심 통계

| 지표 | 값 |
|------|-----|
| 소스 파일 | 3,149개 |
| AST 노드 | 52,578개 |
| 엣지 | 246,301개 |
| 그래프 커뮤니티 | 975개 |
| Rust 크레이트 | 63개 |
| AI 모듈 서브시스템 | 22개 |
| Cargo.toml 의존성 | ~500줄 |