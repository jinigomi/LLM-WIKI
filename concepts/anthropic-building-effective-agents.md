---
title: "Building Effective AI Agents"
source: Anthropic Engineering
date: 2024-12-19
url: https://www.anthropic.com/engineering/building-effective-agents
tags:
  - llm
  - agents
  - engineering
  - anthropic
---

# Building Effective AI Agents

## 핵심 요약

수십 개 팀과의 작업 결과, **가장 성공한 구현은 복잡한 프레임워크가 아닌 단순하고 조합 가능한 패턴**을 사용함.

## 핵심 개념: Workflows vs Agents

| 구분 | Workflows | Agents |
|------|-----------|--------|
| 정의 | 미리 정의된 코드 경로로 도구 조정 | LLM이 동적으로 프로세스와 도구 사용을 관리 |
| 특성 | 예측 가능, 일관성 | 유연성, 모델 중심 의사결정 |
| 적합 상황 | 잘 정의된 작업 | 확장성 필요한 유연한 작업 |

## Workflow 패턴 5가지

### 1. Prompt Chaining
작업을 순차적 단계로 분해, 각 LLM 호출이 이전 출력을 처리.
- **적합 상황**: 작업을 쉽게 분해할 수 있을 때
- **예시**: 마케팅 카피 생성 → 번역

### 2. Routing
입력을 분류하여 특수화된 후속 작업으로 направ.
- **적합 상황**: 명확히 구분되는 범주가 있을 때
- **예시**: 고객 서비스 유형별 분기, 간단/복잡 질문 분리

### 3. Parallelization
동시에 작업하거나 출력을 집계.
- **Sectioning**: 독립적 하위 작업 병렬 실행
- **Voting**: 다양한 출력 위해 동일 작업 여러 번 실행
- **예시**: 가드레일 + 응답 분리, 코드 취약점 검토

### 4. Orchestrator-workers
중앙 LLM이 작업을 동적으로 분해하고 작업자 LLM에 할당.
- **적합 상황**: 하위 작업 예측 불가능할 때 (예: 코드 변경)
- **예시**: 복잡한 코딩 제품, 여러 소스 검색

### 5. Evaluator-optimizer
한 LLM이 응답 생성, 다른 LLM이 평가 및 피드백을 반복 제공.
- **적합 상황**: 명확한 평가 기준이 있고 반복적 개선이 가치 있을 때
- **예시**: 문학 번역, 복잡한 검색 작업

## Agents 구현 핵심 원칙

1. **단순성 유지** - 에이전트 설계는 최대한 단순하게
2. **투명성 확보** - 에이전트의 계획 단계를 명시적으로 표시
3. **ACI (Agent-Computer Interface) 신중 설계** - 도구 문서화와 테스트 철저히

## 프레임워크 관련 조언

> "처음부터 LLM API를 직접 사용하는 것을 추천: 많은 패턴이 몇 줄의 코드로 구현 가능"

프레임워크 사용 시底层 코드를 이해해야 함. 잘못된 가정은 오류의 주요 원인.

## 언제 Agent를 사용할 것인가?

- **사용**: 오픈된 문제, 필요한 단계 수 예측 어려울 때
- **피해야 함**: 더 간단한 솔루션으로 충분할 때 (단일 LLM 호출 + retrieval + in-context 예제로 충분한 경우 많음)

## 실용적 조언

1. 가장 간단한 해결책부터 시작
2. 복잡성이 정말 필요할 때만 증가
3. 에이전트 도입 시 sandbox 환경에서 충분한 테스트
4. 도구 설계에 프롬프트 엔지니어링 만큼의 노력投入

## 출처

[Building Effective AI Agents](https://www.anthropic.com/engineering/building-effective-agents) - Anthropic Engineering, 2024.12.19