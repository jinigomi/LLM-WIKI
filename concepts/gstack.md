# gstack — AI Engineering Workflow

> **URL**: https://github.com/garrytan/gstack  
> **크기**: 거대 (README 38KB, SKILL.md 50KB, CLAUDE.md 38KB)  
> **핵심**: SKILL.md 파일 컬렉션으로 AI 에이전트에게 역할 기반 스킬 부여

## 개요

gstack은 AI 에이전트를 위한 **전문가 역할 시스템**이다. 각 스킬은 CEO reviewer, eng manager, designer, QA lead, release engineer, debugger 등의 전문 분야를 가지고 있다.

```
gstack의 스킬 체계
├── /office-hours      → Start here. 제품 아이디어 재정의
├── /plan-ceo-review   → 10-star product 찾기
├── /plan-eng-review   → 아키텍처/데이터플로우 잠금
├── /plan-design-review → 디자인 평가 (0-10 척도)
├── /design-consultation → 디자인 시스템 구축
├── /review            → PR 리뷰 (production 버그 잡기)
├── /debug             → 체계적 디버깅
├── /design-review     → 디자인 감사 + 수정 루프
├── /qa                → Headless browser로 버그 찾기
├── /ship              → 테스트 + 리뷰 + 푸시 + PR
├── /document-release  → 배포 후 문서 업데이트
├── /retro             → 주간 회고
├── /browse            → Headless Chromium (~100ms/command)
├── /careful           → 위험 명령어 경고
├── /freeze            → 디렉토리 편집 잠금
└── /guard             → careful + freeze 동시 활성화
```

## 핵심 아키텍처

### 1. Template-Driven Skill Generation

```bash
SKILL.md.tmpl → 생성 → SKILL.md
```

- SKILL.md는 **자동 생성**됨
- 템플릿(.tmpl)을 수정하고 `bun run gen:skill-docs` 실행
- Codex-specific 출력: `bun run gen:skill-docs --host codex`

### 2. Preamble System

모든 스킬은 **preamble**으로 시작 — 환경 정보 수집:

```bash
# 세션 관리
mkdir -p ~/.gstack/sessions
_SESSIONS=$(find ~/.gstack/sessions -mmin -120 -type f | wc -l)

# 브랜치 정보
_BRANCH=$(git branch --show-current)

# 라우팅 규칙 확인
_HAS_ROUTING="no"
if [ -f CLAUDE.md ] && grep -q "## Skill routing" CLAUDE.md; then
  _HAS_ROUTING="yes"
fi

# Checkpoint 모드
_CHECKPOINT_MODE=$(gstack-config get checkpoint_mode)  # explicit vs continuous
```

### 3. Safety System

```
careful  → rm -rf, DROP TABLE, force-push 등 위험 명령 경고
freeze   → 특정 디렉토리 편집 차단 (경고가 아닌 차단)
guard    →两者 동시 활성화
```

### 4. Proactive vs Reactive

```bash
PROACTIVE="true"  → 스킬 자동 제안 (스스로 판단)
PROACTIVE="false" → 사용자 명시적 요청만 (/qa, /ship만 실행)
```

### 5. Checkpoint Modes

| Mode | Behavior |
|------|----------|
| `explicit` | 수동 커밋만 (기본값) |
| `continuous` | WIP 커밋 자동 생성 (`WIP:` prefix) |

### 6. Routing System

CLAUDE.md에 `## Skill routing` 섹션 추가 가능:

```markdown
## Skill routing
- If user mentions "review" → /review
- If user mentions "ship" → /ship
```

## Fresh Context 전략

### Session-Based Memory

```bash
~/.gstack/sessions/$PPID  # 현재 세션 파일
# 120분 이내 세션만 유효
find ~/.gstack/sessions -mmin +120 -exec rm {} +
```

### Learnings System

```bash
~/.gstack/projects/${SLUG}/learnings.jsonl
# 5개 이상 항목 시 자동 검색 제안
```

### Timeline Logging

```bash
~/.gstack/timeline/  # 로컬에만 저장 (telemetry 없이)
```

## Context Rot 해결책

### 1. Token 사용량 감지

```bash
# preamble에서 매번 체크
_LEARN_COUNT=$(wc -l < "$_LEARN_FILE")
if [ "$_LEARN_COUNT" -gt 5 ]; then
  gstack-learnings-search --limit 3
fi
```

### 2. 라우팅 규칙

CLAUDE.md에 명시적 라우팅을 두어 **스킬끼리 참조** 가능:

```markdown
## Skill routing
- browse/test → /browse
- review/verify → /review  
- ship/deploy → /ship
```

### 3. Vendoring Detection

```bash
# 프로젝트 내 gstack 복사본 감지
if [ -d ".claude/skills/gstack" ] && [ ! -L ".claude/skills/gstack" ]; then
  _VENDORED="yes"
fi
```

## 스킬 개발자를 위한 인사이트

### gstack이 가르치는 것

1. **Role-Based Architecture**: 스킬을 독립적 역할로 분리
2. **Template-Driven**: 생성된 파일 vs 소스 파일 분리
3. **Safety First**: 위험한 작업에 대한 명시적 방어
4. **Context Awareness**: preamble으로 환경 정보 주입
5. **Persistence via Files**: 세션/학습을 파일로 관리

### 초보자가 배울 점

```
[gstack 사고방식]
1. "코딩 전에 뭘 해야 하는가?" → /office-hours
2. "누가 리뷰하는가?" → Role-based review
3. "어떻게 안전하게 작업하는가?" → freeze/careful
4. "context가 무거워지면?" → checkpoint reset
```

## Similar Projects

- [[superpowers]] — TDD 기반 스킬 시스템
- [[gsd-2]] — 실제 제어형 에이전트
- [[ralph]] — Fresh context loop

## Tags

#skill-system #context-management #role-based #safety #template-driven