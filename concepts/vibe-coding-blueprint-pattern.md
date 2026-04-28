---
created: 2026-04-28
source: "[[app-blueprint-prompt]]"
tags: [methodology, vibe-coding, blueprint, plan-first, AI-collaboration]
---

# Vibe Coding 블루프린트 패턴

> 코드를 쓰기 전에 완전한 설계 문서를 AI가 생성하게 하는 협업 패턴. "plan → build → launch" 사이클을 AI와 함께 돌리는 방법론.

## 정의

**Vibe Coding**에서 가장 흔한 함정은 "바로 코딩 시작"이다. 카파시도 지적했듯, LLM은 맥락 없이 코드를 생성하면 엉뚱한 방향으로 간다.

**블루프린트 패턴**의 핵심 통찰: _코드 한 줄 쓰기 전에 AI에게 완전한 청사진을 그리게 한다._

## 패턴 구조

```
[메타프롬프트: 페르소나 + 기대 산출물 + 품질 기준]
   ↓
[AI가 시장/제품/기술/출시 전략을 포괄하는 APP_BLUEPRINT.md 생성]
   ↓
[블루프린트를 컨텍스트로 코드 작성을 AI에게 위임]
   ↓
[주차별 마일스톤으로 진행 관리]
```

## 핵심 원칙

1. **Think Before Building** — 카파시 가이드라인 1번을 제품 레벨로 확장
2. **Context is Everything** — AI가 올바른 결정을 내리려면 완전한 컨텍스트가 필요
3. **Production-Level Only** — "튜토리얼 수준" 금지, 실제 에러 핸들링/로깅/확장성 포함
4. **Single Prompt Rule** — 하나의 프롬프트로 모든 계획을 AI에게 맡김

## [[karpathy-vibe-coding-pitfalls]]와의 관계

카파시가 지적한 vibe coding 함정:
- "LLM이 내가 생각한 것과 다른 걸 만든다" → 블루프린트가 공유된 정신 모델 역할
- "에러를 못 고친다" → 프로덕션 수준 에러 핸들링이 블루프린트에 포함
- "점점 복잡해진다" → MVP 기능 3~5개로 제한하는 규율

## 한계

- 시장 검증은 AI 추정일 뿐, 실제 시장 리서치가 아님
- 경쟁사 가격/리뷰는 학습 데이터 시점 기준
- AI가 제안한 수익화 모델이 항상 현실적이진 않음

→ 블루프린트는 **출발점**이지 **진실**이 아니다. 사람의 검증이 필요하다.

## 연관 노트

- [[app-blueprint-prompt]] — 실제 메타프롬프트 원문
- [[plan-before-code]] — 코딩 전 계획의 일반 원칙
- [[AI-협업-방법론-체크리스트]] — 초보자를 위한 전체 로드맵
- [[vibe-coding-senior-advisor-prompt]] — 시니어 엔지니어 페르소나 패턴