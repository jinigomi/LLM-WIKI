---
aliases: [Large Repo, Monorepo Git]
title: Large Repository Git Strategy
created: 2026-04-30
updated: 2026-04-30
type: concept
tags: [git, large-repo, monorepo, sparse-checkout, partial-clone, lfs]
sources: [entities/high-performance-git.md]
confidence: high
skill: git-large-repo
---

# Large Repository Git Strategy

> "리포지토리가 크다"는 하나의 문제가 아니다. 4가지 차원에서 클 수 있고, 각각 다른 해결책이 필요하다.

## 4가지 Large 차원

| Dimension | 증상 | 해결책 |
|---|---|---|
| **History deep** | clone 느림, log 느림 | shallow clone, commit-graph |
| **Working tree wide** | status 느림 | sparse-checkout (cone mode) |
| **Blob heavy** | .git 비대, clone 느림 | partial clone, LFS |
| **Ref bloated** | fetch/branch 느림 | reftable, ref 정리 |

## 핵심 명령어

```bash
# Partial clone (blob 필요할 때만 다운로드)
git clone --filter=blob:none <url>

# Sparse-checkout (필요한 디렉토리만)
git sparse-checkout init --cone
git sparse-checkout set src/ docs/

# Reftable (ref lookup O(log n))
git config refs.storageFormat reftable
```

## Agent 환경에서

```bash
# Worktree + sparse-checkout 조합
git worktree add ../agent-feature feature/ui
cd ../agent-feature
git sparse-checkout init --cone
git sparse-checkout set frontend/
```

## 관련

- [[high-performance-git]] — 원서
- Skill: `git-large-repo` — 전체 가이드
- Skill: `git-gc-maintenance` — 유지보수
- Skill: `git-agent-loop` — Agent 용 Git