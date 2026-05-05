---
aliases: [Git Perf, Git Performance]
title: High Performance Git
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [git, performance, book, large-repos, internals, agent-loop]
sources: [https://gitperf.com/, https://github.com/tnm/high-performance-git-pdf]
confidence: high
pdf: raw/high-performance-git.pdf
---

# High Performance Git

> Ted Nyman · Edition 1.1 · 228 pages · CC BY-SA 4.0

Git의 내부 구조와 대형 리포지토리에서의 성능 최적화를 다루는 책. 단순한 Git 사용법이 아니라 **Git이 왜 느려지는지**, 그리고 **어떻게 빠르게 만드는지**를 레이어별로 파헤친다.

## Overview

Git은 하나의 도구처럼 보이지만, 실제로는 여러 시스템이 겹쳐진 계층 구조다:

- **Content-addressed object store** (콘텐츠 주소 지정 객체 저장소)
- **Filesystem cache** (파일시스템 캐시)
- **History graph** (히스토리 그래프)
- **Transfer protocol** (전송 프로토콜)

책의 핵심 명제: *Git 성능은 하나의 숫자가 아니라, 각 명령어가 다른 레이어를 누르기 때문에 발생하는 비용의 집합이다.*

## 책의 구조 (5 Sections + Epilogue)

### I. Foundations
**Git의 논리적 모델 이해**

| Chapter | 주제 | 핵심 내용 |
|---|---|---|
| 1 | Why Git Performance Matters | `git status` ≠ `git log` ≠ `git fetch` — 각 명령은 다른 레이어 사용 |
| 2 | Core Data Model | Blob, Tree, Commit, Tag — Git은 diff가 아니라 **snapshot**을 저장 |
| 3 | Refs, HEAD, Reflogs, Index | 데이터 플레인 vs 컨트롤 플레인 분리 이해 |

### II. History and Rewrite
| Chapter | 주제 | 핵심 내용 |
|---|---|---|
| 4 | Revisions & History Traversal | Path-limited history의 비용, rename detection은 heuristic |
| 5 | Merge, Rebase, Cherry-Pick | `rerere`(재사용 가능한 충돌 해결 기록), `range-diff` |

### III. Storage and Local Scale
| Chapter | 주제 | 핵심 내용 |
|---|---|---|
| 6 | Loose Objects, Packfiles, Delta | Delta compression ≠ commit history; 내부 저장 메커니즘 이해 |
| 7 | Index as Performance Structure | Index는 **성능 구조체** — stat cache, untracked cache, fsmonitor |
| 8 | Commit-Graph, Bloom Filters, MIDX, Bitmaps | 그래프 가속기 4종 — 각각 해결하는 문제가 다름 |
| 9 | GC and Maintenance | `git maintenance` > `git gc` — incremental 전략이 핵심 |
| 10 | Sparse-Checkout & Sparse-Index | Cone mode, working tree 축소 전략 |

### IV. Large-Repo Operations, Transport, and Scale

| Chapter | 주제 | 핵심 내용 |
|---|---|---|
| 11 | Partial Clone & Promisor Remotes | `--filter=blob:none` — 필요할 때만 객체 다운로드 |
| 12 | Scalar, Prefetch, Large Repos | Microsoft의 대형 리포지토리 도구 |
| 13 | Worktrees | 하나의 객체 DB에 여러 작업 디렉토리 |
| 14 | Clone, Fetch, Push, Protocol v2 | 전송 계층 최적화 |
| 15 | Bundles & Bundle URIs | 오프라인/CI 용 번들 전략 |
| 16 | Reducing Repository Size | `git filter-repo`, BFG |
| 17 | Large Ref Sets | Packed-refs, Reftable, `git refs` |

### V. Diagnosis and Recovery
| Chapter | 주제 | 핵심 내용 |
|---|---|---|
| 18 | Instrumenting Git | `GIT_TRACE`, `GIT_TRACE2`, strace/dtrace |
| 19 | Finding & Fixing Slow Git | 5가지 진단 모델 — 어느 레이어가 병목인지 분류 |
| 20 | Configuration Playbook | `core.fsmonitor`, `fetch.writeCommitGraph`, `maintenance.auto` 등 |
| 21 | Recovery & Repair | `git fsck`, `git reflog`, corrupted object 복구 |

### Epilogue: Git in the Agent Loop
Agent-heavy 시대의 Git 사용법:

1. **Worktrees per agent** — `git worktree add`로 에이전트 간 격리
2. **Metadata pressure** — `git notes`, `git interpret-trailers`로 에이전트 메타데이터 관리
3. **Ref scaling** — branch/retry/proposal 폭증에 ref 관리가 더 중요
4. **Rewrite normal** — 많은 작은 커밋 + 반복 rebase가 일상화
5. **Maintenance critical** — 배경 유지보수, commit-graph, Bloom filter가 선택이 아니라 필수
6. **Review shift** — provenance(출처) 기반 리뷰로 전환; `range-diff`, notes가 중심

## Key Insights

### 1. Git 명령어별 병목 지점이 다르다
```
git status  → working tree + index 문제
git log -- <path>  → graph-walk + path-history 문제
git fetch  → negotiation + pack + reachability 문제
git clone  → transfer + materialization 문제
```

### 2. Index는 Git의 가장 중요한 성능 구조체
Index는 다음 스냅샷을 위한 무대일 뿐만 아니라, **path state를 캐싱하는 성능 구조체**이기도 하다. stat cache, untracked cache, fsmonitor integration이 모두 index에 기반한다.

### 3. Commit-Graph와 그 친구들
- **commit-graph**: 커밋 도달성 계산 가속
- **changed-path Bloom filters**: `git log -- <path>` 가속
- **bitmaps**: fetch/clone 시 객체 도달성 계산 가속
- **MIDX (multi-pack-index)**: 여러 packfile에 걸친 객체 조회 가속

### 4. Large = 하나의 문제가 아니다
저장소가 "크다"는 여러 의미가 있다:
- History가 클 때 → shallow clone, commit-graph
- Working tree가 클 때 → sparse-checkout
- Blob payload가 클 때 → partial clone
- Ref가 많을 때 → reftable, `git refs`

### 5. Agent Loop 시대의 Git
머신이 코드를 생산하는 속도로 Git을 사용하려면:
- `git worktree`로 에이전트 격리
- `git maintenance`를 백그라운드에
- `git notes` + trailers로 메타데이터 레이어 구축
- `range-diff`로 패치 시리즈 비교 리뷰
- `git merge-tree`로 working tree 없이 merge 테스트

## 관련 개념

- [[spec-driven-development]] — Agent Loop 시대의 개발 프로세스
- [[subagent-driven-development]] — 여러 에이전트 병렬 작업 패턴
- [[git-agent-loop]] — Agent용 Git 워크플로우
- [[git-performance-diagnosis]] — Git 성능 진단 방법론
- [[git-maintenance-strategy]] — Git 유지보수 전략
- [[large-repository-git-strategy]] — 대형 리포지토리 전략

## 연결된 스킬

- `git-agent-loop` — Agent Loop 시대의 Git 워크플로우 패턴
- `git-diagnose-slow` — 느린 Git 진단 체계
- `git-gc-maintenance` — Git 유지보수 자동화
- `git-large-repo` — 대형 리포지토리 전략

## 별첨

- PDF 원문: raw/high-performance-git.pdf
- 공식 사이트: https://gitperf.com/