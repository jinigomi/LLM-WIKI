---
title: Introducing Contextual Retrieval
source: Anthropic Engineering
date: 2024-09-19
url: https://www.anthropic.com/engineering/contextual-retrieval
tags:
- rag
- inference
- model
- lab
created: '2026-04-25'
type: concept
updated: '2026-04-25'
confidence: high
---

# Introducing Contextual Retrieval

## 핵심 요약

RAG의 retrieval 단계에서 **컨텍스트를 파괴하지 않도록** 각 chunk에 설명적 컨텍스트를 추가. 실패율 49% 감소, reranking 조합으로 67% 감소.

## 전통적 RAG의 문제: 컨텍스트 소실

문서를 작은 chunk로 분할할 때 개별 chunk가 독립적으로 의미를 갖게 되어 배경 정보가 사라짐.

**예시:**
- 질문: "ACME Corp의 2023 Q2 매출 성장률은?"
- Chunk: "The company's revenue grew by 3% over the previous quarter."
- 문제: 이 chunk만으로는 **어느 회사**, **어느 기간**인지 알 수 없음

## 해결책: Contextual Retrieval

두 서브 기술 사용:
1. **Contextual Embeddings** - chunk 앞에 설명적 컨텍스트 추가 후 임베딩
2. **Contextual BM25** - chunk 앞에 설명적 컨텍스트 추가 후 BM25 인덱싱

**변환 예시:**
```
original: "The company's revenue grew by 3% over the previous quarter."

contextualized: "This chunk is from an SEC filing on ACME corp's 
performance in Q2 2023; the previous quarter's revenue was $314 million. 
The company's revenue grew by 3% over the previous quarter."
```

## 구현 방법

Claude에게 각 chunk의 컨텍스트를 생성시킴 (800 토큰 chunk, 8k 토큰 문서 기준):

```prompt
<document>
{{WHOLE_DOCUMENT}}
</document>
Here is the chunk we want to situate within the whole document
<chunk>
{{CHUNK_CONTENT}}
</chunk>
Please give a short succinct context to situate this chunk 
within the overall document for the purposes of improving 
search retrieval of the chunk.
```

## 성능 결과

| 방법 | 실패율 감소 |
|------|-------------|
| Contextual Embeddings만 | 35% (5.7% → 3.7%) |
| Contextual Embeddings + Contextual BM25 | 49% (5.7% → 2.9%) |
| 둘 다 + Reranking | **67%** (5.7% → 1.9%) |

## Reranking 조합

1. 초기 검색으로 top 150 chunk 가져옴
2. Reranker가 각 chunk에 relevance 점수 부여
3. Top 20만 모델에 전달

## 핵심 발견

1. Embeddings + BM25가 embeddings만보다优异
2. **Voyage와 Gemini**의 임베딩이 가장 효과적
3. Top-20 chunks 전달이 Top-5/10보다 효과적
4. Chunk에 컨텍스트 추가가 retrieval 정확도를 크게 향상
5. Reranking은 항상 도움됨
6. 모든 기법을 조합하면 최대 성능

## 비용

Prompt caching으로 100만 토큰당 $1.02 (800토큰 chunk, 8k 토큰 문서 기준)

## 관련 개념

- [[mcp-프로토콜]] — retrieval 단계에서 컨텍스트 보존의 중요성
- [[rss-브리핑-파이프라인]] — 실제 RAG 파이프라인 운영 사례
- [[context-rot]] — 컨텍스트 관리와 관련된 근본적 문제

## 출처

[Introducing Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) - Anthropic Engineering, 2024.09.19