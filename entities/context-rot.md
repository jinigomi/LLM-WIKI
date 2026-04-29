---
title: Context Rot
created: 2026-04-25
updated: 2026-04-26
type: concept
tags: [benchmark, model, inference]
sources: [raw/articles/context-rot.md]
confidence: high
---

# Context Rot

## What it is

LLM이 입력 컨텍스트 길이가 길어질수록 성능이 저하되는 현상. 긴 컨텍스트를 균등하게 처리하지 못하며, 의미적 검색에서 특히 두드러짐.

## Key findings

- **Needle in a Haystack (NIAH)**: 단순 검색에서는 양호하지만 의미적 검색에서 성능 저하
- **18개 LLM 평가**: GPT-4.1, Claude 4, Gemini 2.5, Qwen3 등
- **Contextual degradation**: 입력 길이 증가에 비례하여 성능 저하

## Implications

- 긴 컨텍스트가 항상 더 나은 결과를 보장하지 않음
- [[기억하는-개발]] 패턴에서 컨텍스트 관리의 중요성
- RAG나 검색 증강에서 컨텍스트 관리 전략 필요

## Related
- 오케스트레이션 — 지휘자/현장책임자 분업 패턴
- 엔지니어링-사고-초보자 — 초보자를 위한 사고방식 훈련