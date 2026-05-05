---
title: ProjectsMD
aliases: ["projectsmd", "project.md"]
type: entity
tags: [github, rust, project-management, agent-collaboration, cli]
created: 2026-05-01
status: complete
---

# ProjectsMD

## Source
- **Repository**: https://github.com/am423/ProjectsMD
- **Language**: Rust (70%), Markdown (20%)
- **Version**: 0.1.0
- **License**: MIT

## Overview

ProjectsMD는 AI 에이전트와 인간의 협업을 위한 **단일 파일 프로젝트 관리 CLI** 도구다. `project.md`라는 하나의 마크다운 파일로 프로젝트의 모든 컨텍스트(요구사항, 결정사항, 진행상태, 작업목록)를 캡처한다. 데이터베이스도, 웹앱도, 외부 서비스도 없이 `projectsmd` 바이너리 하나와 `project.md` 파일 하나만으로 작동한다. 에이전트는 어떤 세션에서든 이 파일을 읽는 것만으로 프로젝트의 전체 맥락을 파악하고 바로 작업을 재개할 수 있다.

## Key Concepts

- **단일 파일 진실의 원천 (Single Source of Truth)**: 모든 프로젝트 정보가 `project.md` 하나에 집약된다. 컨텍스트 분산(Context Fragmentation)을 해결하는 것이 핵심 목표.
- **3단계 요구사항 생애주기 (Three-Tier Requirements)**: Validated(출시·검증됨) / Active(현재 범위, 가설) / Out of Scope(명시적 제외). 요구사항은 검증될 때까지 가설로 취급한다.
- **의사결정 결과 추적 (Decision Outcome Tracking)**: 결정을 내릴 뿐만 아니라 그 결정이 맞았는지(✓ Good), 재고가 필요한지(⚠️ Revisit), 판단이 이른지(— Pending)를 추적한다.
- **Current State — 세션 재개 지점**: 가장 중요한 섹션. 에이전트가 이 섹션만 읽어도 현재 단계, 마지막 완료된 작업, 다음 할 일, 차단 요소를 모두 알 수 있다.
- **Evolution 체크리스트**: 프로젝트가 진행됨에 따라 `project.md`가 함께 진화한다. 페이즈 전환 시 요구사항 승격/이동, 설명 업데이트, 결정 결과 검토 등을 자동으로 제안한다.
- **Brownfield 지원**: 기존 코드베이스(=레거시 프로젝트)에도 `--brownfield` 플래그로 `project.md`를 초기화할 수 있다. 기존 코드에서 Validated 요구사항을 추론한다.
- **Agent Skills 호환**: [agentskills.io](https://agentskills.io) 표준 준수. Claude Code, Cursor, Codex, Hermes 등 여러 에이전트 프레임워크에 스킬을 설치할 수 있다.

## Architecture / Structure

**기술 스택**: Rust (clap, serde, pulldown-cmark, dialoguer, colored, similar, chrono)

**바이너리 구조** (추정 — 소스코드 28개 .rs 파일 기반):
- `src/main.rs` — CLI 진입점, 서브커맨드 라우팅
- `src/commands/` — `init`, `validate`, `status`, `next`, `task`, `decide`, `discover`, `phase`, `session`, `diff`, `archive`, `view`, `skill` 각 서브커맨드
- `src/project.rs` — `project.md` 파싱, 프론트매터 추출, 섹션 CRUD
- `src/template.rs` — 템플릿 렌더링, brownfield/greenfield 분기
- `src/skill.rs` — 에이전트 스킬 설치 (agentskills.io 표준)

**데이터 흐름**:
```
projectsmd init  → template.md + 사용자 입력 → project.md 생성
projectsmd status → project.md 파싱 → 상태 집계 → 터미널 출력
projectsmd next   → Current State + Tasks 읽기 → 다음 액션 제안
projectsmd session → 인터랙티브 프롬프트 → project.md 업데이트 (Current State, Session Log, task checkboxes, frontmatter)
```

**핵심 CLI 명령어**:
| 명령어 | 설명 |
|---|---|
| `init` | 새 project.md 생성 (인터랙티브 위저드) |
| `validate` | project.md 사양 준수 검증 |
| `status` | 프로젝트 상태 요약 출력 |
| `next` | 다음 할 일 제안 |
| `task add/done/block` | 작업 관리 |
| `decide` | 주요 결정 기록 |
| `discover` | 발견사항/통찰 기록 |
| `phase` | 프로젝트 단계 전환 |
| `session` | 세션 종료 마무리 (인터랙티브) |
| `diff` | 마지막 세션 이후 변경사항 표시 |
| `archive` | 프로젝트 완료 처리 |
| `view` | project.md를 터미널에서 구문강조 렌더링 |
| `skill install/generate` | 에이전트 스킬 설치/생성 |

**project.md 섹션 구조**:
1. YAML Frontmatter (이름, 상태, 날짜, 소유자 등)
2. What This Is — 현재 정확한 설명 (살아있는 문서)
3. Core Value — 가장 중요한 한 가지
4. Requirements — 3단계: Validated / Active / Out of Scope
5. Context — 배경, 환경, 사전 작업
6. Constraints — 하드 리밋과 그 이유
7. Current State — **세션 재개의 핵심** (매 세션마다 업데이트 필수)
8. Architecture / Design — 기술적 접근 방식
9. Key Decisions — 결정 로그 + 결과 추적
10. Tasks — 페이즈별 체크리스트
11. Discoveries — 발견사항, 함정, 놀라움
12. References — 외부 링크
13. Session Log — 추가 전용 타임라인

## Key Insights

- **컨텍스트 분산(Context Fragmentation)을 파일 분산의 반대 방향에서 해결한다**: 대부분의 프로젝트 관리 도구는 정보를 더 잘게 쪼개고 구조화하려 하지만, ProjectsMD는 오히려 하나의 파일로 뭉치는 전략을 취한다. "읽기 한 번으로 프로젝트 전체를 이해할 수 있어야 한다"는 철학.
- **obra/superpowers와 GSD(Get Shit Done)의 계보**: 이 도구는 superpowers의 에이전트 파이프라인 철학(브레인스토밍 → 계획 → 실행)과 GSD의 PROJECT.md 템플릿, 요구사항 생애주기, 의사결정 결과 추적을 직접 계승했다.
- **에이전트가 읽고 쓰는 문서**: project.md는 인간용 문서가 아니다. 에이전트가 매 세션 시작 시 읽고, 작업 중 업데이트하며, 세션 종료 시 Current State를 갱신하는 **에이전트-네이티브 포맷**이다. `agent-skill.md`는 에이전트가 이 파일을 어떻게 다뤄야 하는지 상세히 지시한다.
- **초기 단계(0.1.0)지만 사양(spec)이 먼저다**: `specification.md` v1.1에 529줄의 정식 명세가 있다. 구현보다 사양이 먼저인 접근법 — 파일 포맷, 필드 정의, 에이전트 지침, 인간 지침, 진화 규칙까지 모두 명세에 포함된다.
- **인터랙티브 CLI 선호**: `dialoguer` 크레이트를 사용한 위저드 스타일 인터랙션을 모든 주요 명령어(`init`, `session`)에서 채택. 에이전트 배치 작업보다 인간-주도 워크플로우에 최적화되어 있다.

## Related Entities

- [[superpowers]] — 에이전트 스킬 파이프라인, 검증-우선 철학, 하위에이전트 주도 개발
- GSD(Get Shit Done) ⚠️ 페이지 미생성 — PROJECT.md 템플릿, 요구사항 3단계 생애주기, 결정 결과 추적의 원류