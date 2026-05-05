---
aliases: [Git Diagnosis]
title: Git Performance Diagnosis
created: 2026-04-30
updated: 2026-04-30
type: concept
tags: [git, performance, diagnosis, trace]
sources: [entities/high-performance-git.md]
confidence: high
skill: git-diagnose-slow
---

# Git Performance Diagnosis

> Git이 느릴 때, 어떤 레이어에서 왜 느린지 진단하는 체계적인 방법.

## 5가지 병목 분류

| 분류 | 증상 | 진단 도구 |
|---|---|---|
| **A. Filesystem Cache** | `git status`, `git diff` 느림 | `GIT_TRACE2_PERF=1`, `strace` |
| **B. History Graph** | `git log`, `git blame` 느림 | commit-graph verify, Bloom filter check |
| **C. Transfer Protocol** | `git fetch`, `git clone` 느림 | protocol version, bitmaps |
| **D. Object Store** | `git add` (대용량), `.git` 비대 | `git count-objects -vH` |
| **E. Ref Scaling** | branch/tag 많을 때 느림 | `git for-each-ref \| wc -l` |

## 진단 원칙

1. **명령어를 좁혀라** — "Git이 느리다"가 아니라 "`git log -- src/`가 10초 걸린다"
2. **레이어를 식별하라** — 그 명령이 어떤 레이어를 사용하는지 파악
3. **TRACE2로 측정하라** — 추측하지 말고 데이터로 진단
4. **레이어별 해결책을 적용하라** — commit-graph는 `git status`를 고치지 않는다

## 관련

- [[high-performance-git]] — 원서
- Skill: `git-diagnose-slow` — 단계별 진단 가이드
- Skill: `git-gc-maintenance` — 진단 후 유지보수 적용
- Skill: `git-large-repo` — 대형 리포지토리 특화 진단