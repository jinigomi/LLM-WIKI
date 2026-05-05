---
title: Obsidian Skills
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [obsidian, agent, tools-infra, agent-framework, skills, agent-workflow]
sources: [https://github.com/kepano/obsidian-skills]
confidence: high
---

## Overview

Obsidian Skills는 Obsidian을 위한 Agent Skills 컬렉션이다. [Agent Skills 스펙](https://agentskills.io/specification)을 따르며, Claude Code, Codex CLI 등 skills-compatible agent에서 사용 가능하다. 제작자는 Obsidian 창시자 [kepano](https://github.com/kepano) (Stephan Ango).

각 스킬은 독립적인 `SKILL.md` + 참조문서로 구성되어 있고, 마켓플레이스 플러그인 형태로 배포된다.

## Architecture

```
obsidian-skills/
├── README.md
├── .claude-plugin/          # 마켓플레이스 등록용
└── skills/
    ├── obsidian-markdown/   # Obsidian Flavored Markdown
    ├── obsidian-bases/      # .base 파일 (데이터베이스 뷰)
    ├── obsidian-cli/        # Obsidian CLI
    ├── json-canvas/         # .canvas 파일 (비주얼 캔버스)
    └── defuddle/            # 웹페이지 → 클린 마크다운 추출
```

## Skills

### 1. obsidian-markdown

Obsidian Flavored Markdown 생성 및 편집. 핵심 기능:

- **Wikilinks** (`page`, ``page``, ``page#heading``)
- **Embeds** (`!note`, `!`image.png``)
- **Callouts** (`> [!note]`, `> [!info]`, 접이식 + nesting)
- **Properties** (YAML frontmatter: `tags`, `aliases`, `cssclasses`, `publish`)
- **Tags** (`#tag`, nested tags `#parent/child`)
- **Footnotes** (`[^1]`)
- **Mermaid diagrams** 지원
- Math (LaTeX) 지원

레퍼런스 파일: `PROPERTIES.md`, `CALLOUTS.md`, `EMBEDS.md`

### 2. obsidian-bases

`.base` 파일로 데이터베이스 뷰 생성.

- **Views**: `table`, `cards`, `list`, `map`
- **Filters**: `and`/`or`/`not` + 연산자 (`==`, `!=`, `>`, `&&`, `||`)
- **Formulas**: `if()`, `date()`, `sum()`, `filter()`, `map()` 등 JavaScript 표현식
- **Summaries**: `Average`, `Sum`, `Min`, `Max`, custom formula
- **Properties**: note properties, file properties (`file.name`, `file.mtime`, `file.tags`), formula properties

### 3. obsidian-cli

Obsidian CLI로 볼트 조작. 주요 명령어:

- **Vault operations**: `obsidian vault-info`, `obsidian open`, `obsidian open --path '...'`
- **Note CRUD**: `obsidian note-create`, `obsidian note-open`, `obsidian note-delete`, `obsidian note-trash`
- **Search**: `obsidian search`, `obsidian search --tags` (by tag), `obsidian search --regex` (by regex)
- **Properties**: `obsidian properties-get`, `obsidian properties-set`, `obsidian properties-delete`
- **Tasks**: `obsidian tasks`
- **Commands**: `obsidian execute-command obsidian.command.name`
- **Plugin 개발**: `obsidian reload-plugin`, `obsidian run-js`, `obsidian console`, `obsidian screenshot`, `obsidian inspect-element`, `obsidian console-capture`, `obsidian list-plugins`

### 4. json-canvas

`.canvas` 파일 생성 및 편집 (JSON Canvas Spec 1.0 기반).

- **Nodes**: `text`, `file`, `link`, `group` 타입
- **Edges**: source→target with optional labels, side anchors, colors
- **Groups**: 시각적 컨테이너로 다른 노드들을 포함
- 16-char hex ID, 6 preset colors (`"1"`-`"6"`) or hex colors

### 5. defuddle

웹페이지에서 클린 마크다운 추출.

```bash
npx defuddle https://example.com/article
```

- 광고, 네비게이션, 사이드바 등 clutter 제거
- 기사/문서/블로그의 본문만 추출
- `.md` URL에는 사용하지 않음 (이미 마크다운이므로 직접 `WebFetch`)

## Key Design Decisions

1. **Agent Skills 스펙 준수** — 에이전트 생태계에 호환성 제공 (Claude Code, Codex 등)
2. **마켓플레이스 배포** — `/plugin marketplace add kepano/obsidian-skills`로 등록, `/plugin install`로 즉시 사용
3. **독립적 구조** — 각 스킬이 완전히 독립적으로 동작하며 레퍼런스 파일 포함
4. **공식 CLI 연동** — `obsidian-cli` 스킬은 Obsidian 공식 CLI를 래핑

## Strengths / Weaknesses

- **강점**: Obsidian 생태계와 깊게 통합. Agent가 볼트를 직접 읽고/쓰고/검색 가능해짐. 공식 CLI + 플러그인 API 완전 커버.
- **약점**: Obsidian CLI 의존성 — CLI 미설치 시 `obsidian-cli` 스킬 사용 불가. 문서 위주 레포라 Graphify AST 분석으로는 의미 있는 구조 파악 불가.

## 연결 개념

- HermesAgent — 이 스킬들을 자체 워크플로우에 통합 가능
- Agent Skills Spec — Obsidian Skills가 따르는 Agent Skills 표준
- vibe-coding — Agent Skills의 실전 활용처
- obsidian — 모든 스킬의 기반이 되는 도구
- defuddle — 웹 스크래핑/클리닝 (별도 개별 스킬로도 존재 가능)