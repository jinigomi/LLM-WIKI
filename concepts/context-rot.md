---
title: "Context Rot: How Increasing Input Tokens Impacts LLM Performance"
source: TryChroma
date: 2025-07-14
url: https://www.trychroma.com/research/context-rot
tags:
  - llm
  - context-window
  - evaluation
  - research
---

# Context Rot

## 핵심 요약

LLM은 긴 컨텍스트를 균등하게 처리하지 못함. 입력 토큰이 증가할수록 성능이 저하됨.

## 주요 발견

- ** Needle in a Haystack(NIAH)**: 단순 검색 작업에서는 좋은 성능을 보이지만, 의미적 검색에서는 성능 저하
- **18개 LLM 평가**: GPT-4.1, Claude 4, Gemini 2.5, Qwen3 등 평가
- **Context Rot 현상**: 입력 길이가 길어질수록 모델 성능이 비례하여 저하됨

## 실험 결과

1. **Needle-Question Similarity**: 의미적으로 관련된 needle에서 성능 저하
2. **Haystack Structure**: 구조화된 컨텍스트에서도同样的 문제
3. **LongMemEval**: 대화형 QA에서 컨텍스트 길이 영향 확인
4. **Repeated Words**: 반복 단어 작업에서도 길이 증가 시 성능 저하

## 함의

- 긴 컨텍스트가 항상 더 나은 결과를 보장하지 않음
- RAG나检索 증强에서 컨텍스트 관리의 중요성
- 모델의 실제 성능은 벤치마크보다 복잡한 실제 시나리오에서 더 저하될 수 있음

## 출처

[Context Rot: How Increasing Input Tokens Impacts LLM Performance](https://www.trychroma.com/research/context-rot)
