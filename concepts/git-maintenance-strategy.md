---
aliases: [Git Maintenance]
title: Git Maintenance Strategy
created: 2026-04-30
updated: 2026-04-30
type: concept
tags: [git, maintenance, gc, commit-graph, midx]
sources: [entities/high-performance-git.md]
confidence: high
skill: git-gc-maintenance
---

# Git Maintenance Strategy

> `git gc`의 시대는 끝났다. `git maintenance` + incremental 전략으로 지속적인 성능을 유지하는 방법.

## Maintenance vs GC

| | `git gc` | `git maintenance` |
|---|---|---|
| 실행 | 수동, one-shot | 자동, incremental |
| 구성 | 하나의 무거운 작업 | 5개의 가벼운 task |
| Agent 환경 | 부적합 (reflog 소실 위험) | 필수 |

## 4대 가속기

1. **commit-graph** — `git log` 가속
2. **Bloom filters** — `git log -- <path>` 가속
3. **MIDX** — 다중 packfile 조회 가속
4. **Bitmaps** — fetch/clone 도달성 계산 가속

## 핵심 명령어

```bash
git maintenance start                    # 자동화 등록
git commit-graph write --reachable --changed-paths  # 커밋 그래프 생성
git multi-pack-index write               # MIDX 생성
git config fetch.writeCommitGraph true   # fetch 후 자동 갱신
```

## 관련

- [[high-performance-git]] — 원서
- Skill: `git-gc-maintenance` — 전체 설정 가이드
- Skill: `git-large-repo` — 대형 repo 유지보수