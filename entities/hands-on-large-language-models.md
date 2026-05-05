---
title: Hands-On Large Language Models
aliases: ["The Illustrated LLM Book", "HOLM"]
type: entity
tags: [book, llm, transformers, tutorial, visualization]
created: 2026-05-01
sources: [raw/articles/hands-on-llm-book.md]
confidence: high
---

# Hands-On Large Language Models

Jay Alammar와 Maarten Grootendorst가 집필한 LLM 실습 입문서. 별칭 **"The Illustrated LLM Book"** — 290개 이상의 커스텀 일러스트로 시각적 이해를 강조. 12개 Colab 노트북 + 9개 보너스 가이드로 구성. 초보자도 사전 NLP 지식 없이 따라갈 수 있게 설계되었음.

- **저자**: [Jay Alammar](https://www.linkedin.com/in/jalammar/), [Maarten Grootendorst](https://www.linkedin.com/mgrootendorst/)
- **출판**: O'Reilly (2024), ISBN 978-1098150969
- **리뷰어**: Andrew Ng, Nils Reimers (sentence-transformers 창시자), Luis Serrano 등
- **플랫폼**: 모든 실습은 Google Colab T4 GPU 기준 (16GB VRAM 무료)

## 구조 (12 Chapters)

책은 크게 3파트로 나뉜다:

### Part 1: 기초 (Ch 1-3) — LLM 이해하기
| Ch | 제목 | 핵심 내용 |
|---|---|---|
| 1 | Introduction to Language Models | 언어 모델 개념, 역사, 오토리그레시브 생성 |
| 2 | Tokens and Embeddings | 토크나이저 원리, 임베딩 공간, 유사도 |
| 3 | Looking Inside Transformer LLMs | 트랜스포머 내부 구조: attention, MLP, residual |

### Part 2: 응용 (Ch 4-9) — LLM 활용하기
| Ch | 제목 | 핵심 내용 | 사용 라이브러리 |
|---|---|---|---|
| 4 | Text Classification | 분류 태스크: representation model vs generative model 접근 | sentence-transformers, transformers, OpenAI API |
| 5 | Text Clustering & Topic Modeling | 문서 군집화, BERTopic, UMAP/HDBSCAN | BERTopic, UMAP, HDBSCAN, sentence-transformers, OpenAI |
| 6 | Prompt Engineering | 효과적인 프롬프트 작성법, few-shot, chain-of-thought | transformers, torch |
| 7 | Advanced Text Generation | LangChain 기반 고급 생성 기법, 도구 통합 | LangChain |
| 8 | Semantic Search & RAG | 의미 기반 검색, 검색 증강 생성 (RAG) | Cohere, LangChain, Faiss, rank-bm25 |
| 9 | Multimodal LLMs | 비전+텍스트 멀티모달 모델 (CLIP, LLaVA 등) | PIL, torch, transformers |

### Part 3: 파인튜닝 (Ch 10-12) — LLM 커스터마이징
| Ch | 제목 | 핵심 내용 | 사용 라이브러리 |
|---|---|---|---|
| 10 | Creating Text Embedding Models | 임베딩 모델 학습/파인튜닝 (Sentence Transformers, MTEB 벤치마크) | sentence-transformers, MTEB, datasets |
| 11 | Fine-tuning BERT for Classification | BERT 분류 태스크 파인튜닝 (SetFit 포함) | transformers, datasets, SetFit |
| 12 | Fine-tuning Generation Models | 생성 모델 파인튜닝 (QLoRA, PEFT, TRL) | transformers, peft, trl, bitsandbytes |

### Bonus Content (9개)
책의 심화 주제를 동일한 시각적 스타일로 확장:
1. **How Transformer LLMs Work** — DeepLearning.AI 코스
2. **A Visual Guide to Quantization** — 양자화 원리 시각화
3. **A Visual Guide to Mamba & State Space Models** — SSM 대안 아키텍처
4. **A Visual Guide to Mixture of Experts (MoE)** — MoE 라우팅 원리
5. **The Illustrated Stable Diffusion** — 이미지 생성 모델
6. **A Visual Guide to Reasoning LLMs** — 추론 능력 메커니즘
7. **The Illustrated DeepSeek-R1** — DeepSeek-R1 분석
8. **A Visual Guide to LLM Agents** — 에이전트 아키텍처

## 핵심 통찰

1. **시각화 우선 접근** — 코드보다 그림으로 개념을 먼저 전달. 290+개의 커스텀 다이어그램이 모든 챕터를 관통한다.
2. **점진적 난이도 상승** — Ch1-3은 개념 소개, Ch4-9는 API 활용, Ch10-12는 직접 파인튜닝. 독자가 자연스럽게 깊이 들어가게 설계.
3. **Colab 중심 실습** — 모든 노트북이 Google Colab에서 즉시 실행 가능. 로컬 환경 설정 부담 없음.
4. **Representation ↔ Generation 대비** — BERT-like (representation)과 GPT-like (generation) 모델을 대비하며 설명하여 두 패러다임을 모두 다룬다.
5. **산업 표준 라이브러리** — HuggingFace 생태계 (transformers, datasets, peft, trl)가 중심. 현업에서 바로 쓸 수 있는 도구를 가르친다.

## 기술 스택

- **핵심 프레임워크**: HuggingFace (transformers, datasets, peft, trl, sentence-transformers)
- **딥러닝**: PyTorch
- **검색/RAG**: LangChain, Cohere, Faiss, rank-bm25
- **군집화/차원축소**: BERTopic, UMAP, HDBSCAN
- **파인튜닝**: QLoRA, PEFT, bitsandbytes, SetFit, MTEB
- **기타**: OpenAI API, tiktoken, NLTK, Matplotlib

## 강점 / 약점

**강점:**
- 비주얼 퀄리티가 압도적 — Transformer 내부 구조, MoE 라우팅, SSM 같은 복잡 개념이 한눈에 이해됨
- Google Colab으로 진입장벽 최소화
- 저자의 Illustrated Transformer, Illustrated Stable Diffusion 등 기존 시리즈와 연계
- 보너스 가이드로 빠르게 발전하는 분야의 최신 토픽도 커버 (Mamba, DeepSeek-R1, Agents)
- Andrew Ng, Nils Reimers 등 업계 리더의 검증을 받음

**약점:**
- 2024년 기준 내용 — 2026년의 최신 발전 (e.g., diffusion LLM, multi-agent orchestration)은 미포함
- 실습이 Colab에 의존적 — 로컬 환경 설정 가이드가 얇음
- 보너스 콘텐츠가 외부 링크로 분산되어 있어 장기간 유지보수 여부가 불확실

## 연결 개념
- [[바이브코딩-책-개요]] — 같은 2024~2025년대 AI 입문서. 바이브코딩 책이 코딩 패턴에 집중한다면, 이 책은 LLM 내부 구조에 집중
- [[high-performance-git]] — Git 내부 구조를 시각적으로 파헤치는 유사한 접근 방식
- Transformer 아키텍처 ⚠️ 페이지 미생성 — Transformer 구조 (향후 위키 페이지)
- RAG 패턴 ⚠️ 페이지 미생성 — RAG 패턴 (향후 위키 페이지)
- 임베딩과 벡터 ⚠️ 페이지 미생성 — 임베딩 (향후 위키 페이지)

## 참고 링크
- [GitHub Repository](https://github.com/HandsOnLLM/Hands-On-Large-Language-Models)
- [O'Reilly Book Page](https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/)
- [DeepLearning.AI Course (Ch2 companion)](https://www.deeplearning.ai/short-courses/how-transformer-llms-work/)
- [저자 블로그 — Jay Alammar](https://jalammar.github.io/)
- [저자 뉴스레터 — Maarten Grootendorst](https://newsletter.maartengrootendorst.com/)