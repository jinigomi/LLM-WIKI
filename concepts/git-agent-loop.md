---
aliases: [Agent Git, Agent Loop Git]
title: Git in the Agent Loop
created: 2026-04-30
updated: 2026-04-30
type: concept
tags: [git, agent-loop, worktree, maintenance, notes, range-diff]
sources: [entities/high-performance-git.md, https://gitperf.com/]
confidence: high
skill: git-agent-loop
---

# Git in the Agent Loop

> Agent가 코드를 생산하는 속도로 Git을 사용하는 방법. 사람 × 기계 협업에서 Git의 역할 변화.

## Core Ideas

### Human vs Agent Git

| Dimension | Human Git | Agent Git |
|---|---|---|
| Working tree | 1개, persistent | N개, worktree per agent |
| Commit rate | 수십/일 | 수백~수천/일 |
| Branch strategy | PR 중심 | branch per task per retry |
| Metadata | commit message only | notes + trailers 구조화 |
| Maintenance | 가끔 `git gc` | `git maintenance` 필수 |
| Review | full diff | `range-diff` (diff of diffs) |

### 에이전트에 맞는 Git 원칙

1. **Worktree 격리** — 에이전트 간 index/status 충돌 방지
2. **Maintenance 자동화** — commit-graph, Bloom filter, MIDX가 선택이 아니라 기본
3. **Provenance 메타데이터** — 어떤 agent가 어떤 task에서 만든 커밋인지 추적
4. **Rewrite가 normal** — 많은 작은 커밋 + 반복 rebase
5. **Range-diff 리뷰** — "무엇이 바뀌었는지"만 추적하는 리뷰 패턴

## 구현 도구

- **`git worktree`** — 에이전트 isolation
- **`git maintenance`** — 백그라운드 incremental 유지보수
- **`git notes`** — 커밋에 메타데이터 추가 (history 수정 없이)
- **`git interpret-trailers`** — 구조화된 trailer 추가/조회
- **`git range-diff`** — 패치 시리즈 비교
- **`git merge-tree`** — working tree 없는 merge 테스트
- **`GIT_TRACE2`** — 성능 진단

## 관련

- [[high-performance-git]] — 원서
- [[subagent-driven-development]] — subagent 구현 워크플로우
- Skill: `git-agent-loop` — 이 개념의 실행 가능한 구현