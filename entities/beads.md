---
title: beads
created: 2026-05-01
updated: 2026-05-01
type: entity
tags: [agent-framework, orchestration, multi-agent, developer-tools, open-source]
sources: [https://github.com/gastownhall/beads]
confidence: high
---

# beads (`bd`)

Steve Yegge(gastownhall)가 만든 **AI 에이전트 전용 분산 그래프 이슈 트래커**.
Dolt를 백엔드로 사용해 Git처럼 버전 관리되는 SQL DB 위에서 의존성 그래프 기반 작업 관리를 제공한다.

## Overview

- **저자:** Steve Yegge ([@steve_yegge](https://x.com/steve_yegge))
- **저장소:** [github.com/gastownhall/beads](https://github.com/gastownhall/beads)
- **언어:** Go 71%, TypeScript 19%, Shell 4%
- **라이선스:** MIT
- **규모:** 1,109 파일 · 11,195 노드 · 40,714 엣지 · 154 커뮤니티 (Graphify 분석)

## 핵심 특징

### 해시 기반 ID (`bd-a1b2`)
모든 이슈가 짧은 해시 ID를 가짐. 멀티에이전트·멀티브랜치 환경에서 ID 충돌이 원천적으로 발생하지 않음. `git`의 커밋 해시와 동일한 철학.

### 3계층 아키텍처

| 계층 | 구성 | 역할 |
|------|------|------|
| **CLI** | Cobra 기반 | 사용자/에이전트 명령 인터페이스 |
| **DB** | Dolt (Embedded / Server 2모드) | Git-like 버전 관리 SQL, 모든 쓰기 자동 커밋, 셀 레벨 머지 |
| **Remote** | DoltHub / S3 / GCS | 원격 동기화 및 협업 |

### 4가지 의존성 타입
- `blocks` — 선행 작업 차단
- `related` — 일반 연관
- `parent-child` — 계층 구조
- `discovered-from` — 분석 중 발견된 연관

### Compaction
완료된 이슈를 의미론적으로 요약하여 컨텍스트를 절약. 에이전트가 처리한 작업의 핵심만 남기고 나머지는 압축.

### Wisps & Molecules
- **Wisps:** 템플릿 기반 워크플로우 단위
- **Molecules:** 여러 Wisps의 조합. **로컬 전용** — 원격 동기화되지 않으며 하드 삭제 라이프사이클을 가짐

### MCP 서버 내장
`integrations/beads-mcp/` 디렉토리에 MCP 서버 구현 포함. AI 에이전트가 직접 beads 이슈를 생성·조회·수정 가능.

## Graphify 분석

- **God Nodes:** `contains()` (947), `Append()` (934), `cleanup()` (588) — CLI 파싱·출력 처리 중심
- **Community Structure:** 154개 커뮤니티로 높은 모듈성 — Dolt SQL 레이어, CLI 명령어, MCP 통합, 타입 시스템이 명확히 분리

## Hermes와의 관계

beads는 **구조화된 작업 관리**(의존성 그래프·상태 머신·감사 추적), Hermes는 **비정형 지식 관리**(메모리·스킬·크론)에 강점. 상호 보완적이며 beads의 MCP 서버를 통해 Hermes와 직접 연동 가능.

| 차원 | beads | Hermes |
|------|-------|--------|
| **목적** | 구조화된 이슈/작업 추적 | 비정형 지식·스킬 누적 |
| **백엔드** | Dolt (Git-like SQL) | Memory + Skills + Sessions |
| **ID 체계** | 해시 기반 (`bd-a1b2`) | 자동 생성 |
| **동시성** | 브랜치·머지 네이티브 | 단일 세션 |
| **에이전트 연동** | MCP 서버 내장 | MCP 클라이언트 |

## 강점
- Git 브랜치·머지 모델을 이슈 트래킹에 적용 — 충돌 없는 병렬 작업
- Dolt의 셀 레벨 머지로 세밀한 충돌 해결
- Compaction으로 에이전트 컨텍스트 윈도우 최적화
- Molecules: 로컬 전용·하드 삭제로 민감한 중간 작업물 보호

## 약점
- Dolt 의존성 — 학습 곡선, 운영 복잡도
- Go 기반이라 Python/JS 생태계 에이전트와의 통합에 추가 레이어 필요 (MCP로 해결)
- 아직 초기 단계 — 커뮤니티·생태계 미성숙

## 연결 개념
- [[subagent-driven-development]] — beads의 의존성 그래프는 subagent 작업 분배에 적합
- [[오케스트레이션]] — beads는 멀티에이전트 작업 조율을 위한 구조화된 기반
- [[hermes-memory-architecture-analysis]] — beads의 Compaction vs Hermes의 Memory 비교
- [[mcp-프로토콜]] — beads의 MCP 서버 내장으로 에이전트 연동
- [[기억하는-개발]] — beads의 버전 관리 이슈 트래킹은 "기억하는" 인프라의 한 축