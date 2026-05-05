---
title: git-sync
created: 2026-05-01
updated: 2026-05-01
type: entity
tags: [tool, mirroring, git, relay, go, cli, embedding]
sources: [raw/articles/git-sync-repo-analysis.md]
---

## Overview

`git-sync`는 Entire.io에서 만든 **remote-to-remote Git 미러링 도구**다. 소스 리모트의 ref를 타겟 리모트로 복사할 때 **로컬 체크아웃을 만들지 않고**, 인메모리 `go-git` 객체 저장소와 smart HTTP 프로토콜만 사용한다. 첫 공개 릴리스는 v0.4.2 (2026-04-30).

핵심 아이디어: "Relay First" — 대부분의 작업을 **팩 릴레이(pack relay)** 로 처리해서 소스의 `upload-pack` 출력을 타겟의 `receive-pack`으로 직접 스트리밍한다. 로컬에서 객체 그래프를 풀지 않기 때문에 리포지토리 크기와 무관하게 메모리가 bounded 상태를 유지한다.

Go 1.26.2+ 필요. CLI + Go 라이브러리로 제공. MIT 라이선스.

## Architecture

```
┌─────────────────────────────────────────────────┐
│  CLI (cmd/git-sync)                             │
│  sync | replicate | plan | bootstrap | probe    │
├─────────────────────────────────────────────────┤
│  Public API (client.go, types.go)               │
│  Client → Probe / Plan / Sync / Replicate       │
│  stable embedding + AuthProvider injection      │
├─────────────────────────────────────────────────┤
│  internalbridge → internal/syncer               │
│  orchestration hub: config → planning → execute │
├──────────┬────────────────┬────────────────────┤
│ planner  │ strategy/      │ gitproto/          │
│  desired │  bootstrap     │  smart HTTP        │
│  plans   │  incremental   │  pkt-line          │
│  ancestry│  materialized  │  pack relay        │
│  pruning │  replicate     │  capability neg.   │
├──────────┴────────────────┴────────────────────┤
│  validation/  │  convert/  │  auth/            │
│  input norm.  │  plan→cmd  │  token+credential │
└─────────────────────────────────────────────────┘
```

### 패키지 레이어

| Layer | Package | 역할 |
|-------|---------|------|
| **Public** | `gitsync` (root) | 안정적인 embedding API — `Probe`, `Plan`, `Sync`, `Replicate` |
| **Unstable** | `unstable/` | CLI/벤치마크용 고급 제어 (Bootstrap, Fetch, batching) |
| **Internal bridge** | `internalbridge/` | public API → internal syncer 연결 |
| **Orchestration** | `internal/syncer` | 전략 선택, 실행, 결과 정형화 |
| **Planning** | `internal/planner` | desired refs 계산, action plan 생성, ancestry 체크 |
| **Strategy** | `internal/strategy/{bootstrap,incremental,materialized,replicate}` | 실제 전송 전략 구현 |
| **Protocol** | `internal/gitproto` | smart HTTP, pkt-line, fetch/push 요청, capability 협상 |
| **Support** | `internal/{validation,convert,auth}` | 입력 검증, plan→push 명령 변환, 인증 |

### 전략 (Strategy) 계층

git-sync의 핵심 설계 결정은 **전략의 명시적 분리**:

| 전략 | 트리거 | 동작 방식 | 메모리 |
|------|--------|-----------|--------|
| **Bootstrap** | 타겟이 비어 있음 | Relay로 소스 전체 팩 → 타겟 스트리밍. `--target-max-pack-bytes`로 배치 가능 | O(1) |
| **Incremental Relay** | fast-forward 브랜치 업데이트, 새 태그 생성 | 소스 `upload-pack` → 타겟 `receive-pack` 직접 스트리밍 | O(1) |
| **Materialized** | force, prune, delete, tag retarget 등 relay 불가 | 로컬 인메모리 `go-git` store에서 객체 클로저 계산 → 인코딩 → push. `--materialized-max-objects` guardrail | bounded by diff |
| **Replicate** | `--mode replicate` | source-authoritative 덮어쓰기. relay-only (fallback 없음). 실패 시 `sync`로 재시도 권고 | O(1) |

### Relay Eligibility

Relay가 가능한 조건:
- 모든 업데이트가 fast-forward 브랜치 또는 새 태그
- force, prune, delete, tag retarget 없음
- 타겟이 `receive-pack`의 relay 기능 지원

이 조건을 만족하지 못하면 materialized fallback으로 빠진다. Materialized 경로는 `MaxAncestryDepth = 2,000,000` 커밋까지 BFS로 ancestry 검사.

## Key Design Decisions

- **Relay First** — 로컬 객체 그래프를 생성하지 않는 relay를 기본 전략으로, materialized는 fallback으로 설계. 배포 환경의 로컬 스토리지 의존성을 제거.
- **Explicit Strategy Split** — bootstrap, incremental relay, materialized, replicate가 각각 분리된 전략 모듈로 구현. "숨겨진 최적화"가 아니라 명시적 경로.
- **Plan-then-Execute** — `plan` 명령으로 실제 push 전에 어떤 ref에 어떤 action이 발생할지 미리 확인 가능. `--dry-run`과 별도.
- **Worker-oriented API** — embedding caller가 AuthProvider와 HTTP client를 주입하는 구조. CLI 의존성 없음.
- **No SSH** — smart HTTP/HTTPS만 지원. SSH 미지원 (의도적 결정).
- **Protocol v2 (source-side only)** — `--protocol auto`로 소스는 v2 우선 협상, push는 v1 `receive-pack` 유지.
- **In-memory only** — materialized fallback도 인메모리 `go-git` store만 사용. 디스크에 클론하지 않음.

## Commands

| 명령 | 설명 |
|------|------|
| `git-sync sync` | 소스 → 타겟 미러링. 부트스트랩 + 증분 + materialized fallback 자동 선택 |
| `git-sync replicate` | source-authoritative 덮어쓰기. relay-only, materialized 없음 |
| `git-sync plan` | `sync`나 `replicate`의 실행 계획 미리보기 (JSON 출력 지원) |
| `git-sync bootstrap` | 빈 타겟 초기 시드 (unstable surface) |
| `git-sync probe` | 소스/타겟 ref 및 capability 조회 |
| `git-sync bench` | 벤치마크 도구 (별도 바이너리) |

## Key Flags

- `--branch main,release` — 특정 브랜치만 동기화
- `--map main:stable` — 소스 브랜치를 다른 타겟 브랜치 이름으로 매핑
- `--tags` — 태그 포함
- `--prune` — 소스에 없는 관리 대상 ref를 타겟에서 삭제
- `--force` — non-fast-forward 업데이트, tag retarget 허용
- `--json` — 머신 리더블 JSON 출력
- `--protocol auto\|v1\|v2` — 소스 측 프로토콜 협상
- `--materialized-max-objects` — materialized fallback의 객체 수 상한
- `--target-max-pack-bytes` — 대규모 부트스트랩 시 팩 분할 크기
- `--measure-memory` — 경과 시간 + Go heap 사용량 샘플링

## Auth

인증 우선순위 (순서대로):
1. CLI 플래그 (`--source-token`, `--target-token`)
2. 환경 변수 (`GITSYNC_SOURCE_TOKEN`, `GITSYNC_TARGET_TOKEN`, `GITSYNC_SOURCE_BEARER_TOKEN`, `GITSYNC_TARGET_BEARER_TOKEN`)
3. `git credential fill` 헬퍼
4. 익명 접근

## Library API

Go 라이브러리로도 사용 가능:

```go
import "entire.io/entire/git-sync"

client := gitsync.New(gitsync.Options{
    HTTPClient: http.DefaultClient,
    Auth:       gitsync.StaticAuthProvider{...},
})
result, err := client.Sync(ctx, gitsync.SyncRequest{
    Source: gitsync.Endpoint{URL: "https://..."},
    Target: gitsync.Endpoint{URL: "https://..."},
    Scope:  gitsync.RefScope{Branches: []string{"main"}},
    Policy: gitsync.SyncPolicy{IncludeTags: true, Protocol: gitsync.ProtocolAuto},
})
```

`unstable` 패키지는 `Bootstrap`, `Fetch`, batching 제어 등 고급 기능을 제공하지만 API 안정성을 보장하지 않는다.

## Key Dependencies

- **go-git v6** (`github.com/go-git/go-git/v6 v6.0.0-alpha.2`) — pure Go Git 구현체. 인메모리 storage, 객체/참조 조작, protocol plumbing
- **go-billy v6** — 파일시스템 추상화 (go-git 의존)
- **go-keyring** — OS 키체인 기반 credential 저장
- **testify** — 테스트

## Stats

- **언어:** Go 100%
- **소스 파일:** 68개 `.go`
- **코드 라인:** ~21,900 lines
- **의존성:** 4 direct + 17 indirect
- **첫 릴리스:** v0.4.2 (2026-04-30)

## Strengths / Weaknesses

- **강점:**
  - Relay-first 설계로 대용량 리포지토리에서도 메모리 bounded
  - `plan` 명령으로 실행 전 계획 확인 → CI/CD 파이프라인에 안전하게 통합 가능
  - Library API 제공 → Go 서비스에 내장 가능
  - 명시적 전략 분리 → 동작 예측 가능, 디버깅 용이
  - front-loaded validation → push 전에 모든 ref 충돌 감지
- **약점:**
  - SSH 미지원 (smart HTTP only)
  - 양방향 동기화 불가 (one-way only)
  - 데몬/워치 모드 없음 (cron/CI에서 직접 호출 필요)
  - materialized fallback은 인메모리이므로 매우 큰 diff에는 부적합
  - go-git v6 alpha 의존 — 안정성 리스크

## 연결 개념

- [[git-agent-loop]] — Agent 시대의 Git 워크플로우. git-sync는 Agent가 관리하는 리포지토리 간 미러링에 활용 가능
- [[git-performance-diagnosis]] — Git 병목 진단. git-sync의 relay 경로는 `git clone` + `git push` 시간에 바운드됨
- [[large-repository-git-strategy]] — 대형 리포지토리 전략. git-sync의 relay 경로는 대형 리포지토리 복제 문제를 우회
- [[mcp-프로토콜]] — MCP 프로토콜. git-sync의 smart HTTP 직접 구현은 MCP의 transport 계층과 유사한 저수준 프로토콜 제어 패턴
- [[high-performance-git]] — Git 성능 최적화 책. git-sync의 relay 접근법은 이 책의 "불필요한 로컬 클론 회피" 원칙과 일치