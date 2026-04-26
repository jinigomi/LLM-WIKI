---
title: "Best Practices for Claude Code"
source: Anthropic Engineering
date: 2025-04-18
url: https://www.anthropic.com/engineering/claude-code-best-practices
tags:
  - llm
  - claude-code
  - engineering
  - anthropic
---

# Best Practices for Claude Code

## 핵심 원칙

> **가장 중요한 리소스: 컨텍스트 윈도우**
> LLM 성능은 컨텍스트가 차면 저하됨. Claude Code 사용의 가장 중요한 제약사항.

## 1. 작업 검증 가능하게 만들기 (최고 효과)

테스트, 스크린샷, 예상 출력을 제공하여 Claude가 스스로 확인할 수 있게 함.

| Before | After |
|--------|-------|
| "이메일 검증 함수 작성" | "validateEmail 함수 작성. 테스트: user@example.com = true, invalid = false. 실행 후 테스트 돌려보기" |
| "대시보드 개선" | "[스크린샷] 이 디자인 구현. 결과 스크린샷 찍어서 비교, 차이점列出하고 수정" |
| "빌드 실패" | "[에러 메시지] 이 에러로 빌드 실패. 해결 후 빌드 성공 확인" |

## 2. 탐색 → 계획 → 코딩 순서

Plan Mode로 연구/계획과 구현을 분리. 잘못된 문제를 푸는 것을 방지.

```
1. Explore: Plan Mode에서 파일 읽고 질문 (변경 없음)
2. Plan: 구현 계획 생성
3. Implement: Normal Mode로 코딩, 계획대로 검증
4. Commit: 설명적 메시지로 커밋 + PR
```

**Plan Mode가 유용한 경우:**
- 접근 방식 불확실할 때
- 여러 파일 수정할 때
-陌生的 코드 다룰 때

**Plan Mode 불필요:**
- 범위 명확, 수정 작을 때 (오타 수정, 로그 추가 등)

## 3. 구체적 컨텍스트 제공

| Before | After |
|--------|-------|
| "foo.py에 테스트 추가" | "foo.py 로그아웃 상태 엣지 케이스 테스트. Mock 방지" |
| "ExecutionFactory 왜 이렇게 이상해?" | "ExecutionFactory git 히스토리 보고 API가 어떻게 만들어졌는지 요약" |
| "캘린더 위젯 추가" | "home page现有 위젯 패턴 참고. HotDogWidget.php 예제 따라 만들기" |

## 4. CLI 도구 활용

```bash
# MCP 서버 연결
claude mcp add server-name

# 긴 출력 페이지 탐색
claude

# 대화 재개
claude --resume

# 토큰 사용량 추적
```

## 5. 컨텍스트 적극 관리

- **토큰 사용량 추적**: 상태 표시줄로 모니터링
- **빈번한 작업**: 작은 단위로 분리
- **대화 재개**: `--resume`로 이전 대화 이어가기
- ** Checkpoint로 되돌리기**: 문제 발생 시 특정 시점부터 재시작

## 6. Auto Mode로 자율 실행

```bash
claude --auto
```

인증을 건너뛰고 자동으로 작업 수행. 테스트 실행, 빌드 모니터링에 유용.

## 7. 하위 에이전트로 Investigation 분산

```bash
claude
  /subagent "read src/auth and summarize session flow"
```

여러 하위 에이전트를 동시에 실행하여 병렬 조사.

## 8. 자주 보는 실패 패턴 피하기

1. **모호한 작업 범위** → 구체적 파일/시나리오 지정
2. **검증 기준 없음** → 테스트/스크린샷 항상 제공
3. **컨텍스트 과적재** → 작업 분리, 주기적 `/clear`
4. **도구 미사용** → MCP 서버 연결해서 자동화

## 9. CLAUDE.md 효과적으로 작성

프로젝트 루트에置いて Claude의 동작 방식 결정:

```markdown
# 프로젝트 설명
# 빌드/테스트 명령
# 코딩 컨벤션
# 자주 하는 작업의 패턴
```

## 핵심 메시지

> "Claude의 자율성은 배울 곡선이 있음. Claude Code를 이해하고, 검증 기준을 제공하면 성능이 극대화됨."

## 출처

[Claude Code: Best practices for agentic coding](https://www.anthropic.com/engineering/claude-code-best-practices) - Anthropic Engineering, 2025.04.18