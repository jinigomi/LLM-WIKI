---
title: "Deep Learning with Python, Third Edition"
created: 2026-05-05
updated: 2026-05-05
type: entity
tags: [book, deep-learning, keras, pytorch, jax, tensorflow, python, transformers, llm, generative-ai, computer-vision, nlp, time-series]
sources: [raw/articles/deep-learning-with-python-3e-toc.md]
---

## 개요

François Chollet(Keras 창시자)와 Matthew Watson(Google Keras 핵심 메인테이너)이 쓴 딥러닝 교과서 제3판. Manning 출판 (MEAP), ISBN 9781633436589.

**온라인 무료 읽기:** https://deeplearningwithpython.io/chapters/ — 20개 챕터 전체의 TOC와 챕터 제목 공개. 개별 챕터 페이지는 403 접근 제한.

핵심 철학: **"손으로 코딩하며 배우기 (hands-on, code-first)"**. 수식은 직관적으로 설명하며, Keras/PyTorch/JAX/TensorFlow 4개 프레임워크 모두 지원.

```
Authors: François Chollet, Matthew Watson
Publisher: Manning | ISBN: 9781633436589
GitHub: github.com/fchollet/deep-learning-with-python-notebooks (★20K)
Edition: 3rd (2024~) — Keras 3, LLM, 생성형 AI 전체 coverage
```

## 목차 (20 챕터)

### Part 1 — 기초
| Ch | 제목 |
|----|------|
| 1 | What is deep learning? |
| 2 | The mathematical building blocks of neural networks |
| 3 | Introduction to TensorFlow, PyTorch, JAX, and Keras |

### Part 2 — 실무 foundations
| Ch | 제목 |
|----|------|
| 4 | Classification and regression |
| 5 | Fundamentals of machine learning |
| 6 | The universal workflow of machine learning |

### Part 3 — Keras 심화
| Ch | 제목 |
|----|------|
| 7 | A deep dive on Keras |

### Part 4 — Computer Vision
| Ch | 제목 |
|----|------|
| 8 | Image classification |
| 9 | ConvNet architecture patterns |
| 10 | Interpreting what ConvNets learn |
| 11 | Image segmentation |
| 12 | Object detection |

### Part 5 — 시계열·텍스트
| Ch | 제목 |
|----|------|
| 13 | Timeseries forecasting |
| 14 | Text classification |

### Part 6 — Transformer·생성형 AI
| Ch | 제목 |
|----|------|
| 15 | Language models and the Transformer |
| 16 | Text generation |
| 17 | Image generation |

### Part 7 — 마무리
| Ch | 제목 |
|----|------|
| 18 | Best practices for the real world |
| 19 | The future of AI |
| 20 | Conclusions |

## 제3판 변경점 (vs 제2판)

| 항목 | 제2판 | 제3판 |
|------|-------|-------|
| Keras 버전 | Keras 2 | **Keras 3** (다중 백엔드) |
| 프레임워크 | TensorFlow only | **TensorFlow + PyTorch + JAX** |
| Transformer | 간단 소개 | **15챕터 전체 devoted** |
| 생성형 AI | GAN 중심 | **LLM + Stable Diffusion + 이미지 생성** |
| GitHub 노트북 | 16개 | 기존 노트북 기반 + 확장 |

## GitHub 저장소

**fchollet/deep-learning-with-python-notebooks** — ★20,063, Fork 9,042
- 16개 Jupyter 노트북 (챕터 2~18, 챕터 1, 6, 19, 20 미포함)
- 코드 예제 + 데이터셋
- 제2판 기준이나 제3판 기초 자료로 활용 가능

```
chapter02_mathematical-building-blocks.ipynb (29KB)
chapter03_introduction-to-ml-frameworks.ipynb (35KB)
chapter07_deep-dive-keras.ipynb (47KB) ← 최대
chapter15_language-models-and-the-transformer.ipynb (31KB)
chapter16_text-generation.ipynb (28KB)
chapter17_image-generation.ipynb (25KB)
```

## 저자 정보

**François Chollet** — Keras 창시자, Ndea 공동 창업자. 딥러닝 + 소프트웨어 엔지니어링 교차점 전문가.

**Matthew Watson** — Google 소프트웨어 엔지니어 (Gemini), Keras 핵심 메인테이너.

## 접근 상태

- **TOC 페이지:** https://deeplearningwithpython.io/chapters/ — 공개 (200 OK)
- **개별 챕터:** 403 Forbidden — Manning MEAP paywall
- **GitHub 노트북:** 공개 — 코드 예제만, 서적 본문은 아님

## 관련 Wiki 페이지

- [[hands-on-large-language-models]] — Jay Alammar의 LLM 그림 교과서 (LLM 시각화 학습)
- [[knowledge-graph-code-intelligence]] — Graphify (AST 분석) — 코드 인텔리전스와 연결