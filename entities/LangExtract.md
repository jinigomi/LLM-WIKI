---
title: LangExtract
aliases: [LangExtract, google-langextract]
type: entity
tags: [github, python, llm, extraction, google, nlp, medical-nlp]
created: 2026-04-30
status: complete
---

# LangExtract

## Source
- **Repository**: https://github.com/google/langextract
- **Language**: Python (≥3.10)
- **Version**: 1.3.0
- **License**: Apache 2.0
- **DOI**: 10.5281/zenodo.17015089
- **Authors**: Akshay Goel (Google HAI-DEF)
- **Stars**: N/A

## Overview
LangExtract는 비정형 텍스트 문서(임상 노트, 리포트, 문학 텍스트 등)에서 LLM을 이용해 구조화된 정보를 추출하는 Python 라이브러리입니다. Few-shot example 기반으로 작동하며, **Precise Source Grounding**(모든 추출 결과를 원본 텍스트의 정확한 위치에 매핑), **Parallel + Multi-Pass extraction**(장문 문서용 청킹 + 병렬 처리 + 복수 패스), **Interactive HTML Visualization**을 핵심 차별점으로 제공합니다. Google Gemini를 기본 권장 모델로 하며, Ollama, OpenAI 등 다양한 LLM을 지원합니다.

## Key Concepts
- **Few-Shot Extraction**: 사용자가 `ExampleData` 객체로 예시를 제공하면, LangExtract가 이를 기반으로 스키마를 생성하고 LLM 추론을 가이드합니다. 프롬프트는 자동 검증(`prompt_validation`)까지 지원.
- **Source Grounding**: 각 추출 결과(`Extraction`)는 `char_interval`을 통해 원본 문서의 정확한 문자 위치에 매핑됩니다. Fuzzy alignment(LCS 기반)로 LLM이 정확히 일치하지 않는 텍스트를 출력해도 위치를 찾아냅니다.
- **Multi-Pass Extraction**: `extraction_passes > 1`로 설정하면 동일 문서를 여러 번 처리하여 recall을 높입니다. 중첩 extraction은 first-pass wins 전략으로 병합.
- **Chunking + Parallelism**: 장문 문서는 tokenizer 기반 청크 → 배치 단위로 병렬 LLM 호출 (`batch_length` + `max_workers`)로 처리.
- **Interactive Visualization**: `.jsonl` 저장된 extraction 결과를 `lx.visualize()`로 self-contained HTML 생성. 원본 텍스트 위에 추출 결과를 highlight + legend로 표시.
- **Provider Plugins**: `langextract.providers` entry-points를 통한 커스텀 provider 등록. `lx.create_plugin` CLI로 보일러플레이트 생성, pip으로 배포 가능.

## Architecture / Structure

### Core Pipeline
```
Input (text | URL | Document[])
  → lx.extract()
    1. PromptValidation (few-shot alignment check)
    2. factory.create_model() → BaseLanguageModel
    3. PromptTemplateStructured 구성
    4. Annotator.annotate_text() / annotate_documents()
       - chunking (TokenChunkIterator)
       - parallel inference (batch_length + max_workers)
       - Resolver: raw LLM output → structured Extraction[]
       - fuzzy alignment (LCS-based, 위치 매핑)
    5. merge non-overlapping extractions (multi-pass)
  → AnnotatedDocument[] (with extractions + char intervals)
  → lx.io.save_annotated_documents() (.jsonl)
  → lx.visualize() (interactive HTML)
```

### Layers (from Graphify communities, cohesion ≥ 0.04)

| Layer | Modules | Cohesion | Description |
|---|---|---|---|
| Extraction API | `extraction.py`, `annotation.py` | 0.02-0.04 | Public API + annotation pipeline |
| Model Factory | `factory.py`, `ModelConfig` | 0.02 | create_model() → provider instantiation |
| Inference | `inference.py`, `GeminiLanguageModel`, `BaseLanguageModel` | 0.03 | LLM inference + batch processing |
| Chunking | `chunking.py`, `core/tokenizer.py` | 0.03 | Text → token chunks for long documents |
| Providers | `providers/gemini.py`, `providers/ollama.py`, `providers/openai.py` | — | LLM provider implementations |
| Schema | `schema.py`, `providers/schemas/gemini.py` | 0.04 | Structured output schema (Gemini controlled generation) |
| Resolver | `resolver.py` (1192 LOC) | — | LLM output parsing + fuzzy alignment |
| Format Handler | `core/format_handler.py` | 0.03 | JSON/YAML format + code fence handling |
| Data Layer | `core/data.py` (Extraction, Document, CharInterval, ExampleData) | 0.04 | Core data types |
| Visualization | `visualization.py` | 0.09 | HTML + highlight + legend generation |
| IO | `io.py`, `data_lib.py` | 0.04 | JSONL read/write, URL fetching, Dataset |
| Benchmarks | `benchmarks/` | 0.05 | Extraction benchmarking + plots |
| Plugin System | `plugins.py`, `providers/registry.py` | 0.1 | Provider plugin discovery + CLI scaffolding |

### Core Hubs (God Nodes, by betweenness centrality)
1. **Extraction** (87 edges) — 핵심 데이터 클래스. 모든 추출 결과가 이 타입으로 표현됨
2. **extract()** (80 edges) — 유일한 public API. 모든 파라미터가 여기로 수렴
3. **ExampleData** (63 edges) — few-shot example 주입 지점
4. **ModelConfig** (44 edges) — 모델 설정의 중심
5. **Resolver** (40 edges) — LLM raw output → structured data 변환
6. **ScoredOutput** (40 edges) — extraction + 품질 점수
7. **create_model()** (36 edges) — provider factory 함수

### Import Contracts (enforced by import-linter)
- `providers` → `inference` 금지 (provider가 inference 로직에 의존 불가)
- `core` → `providers` 금지 (코어가 provider에 의존 불가)
- `core` → `annotation`, `chunking`, `prompting`, `resolver` 금지 (고수준 모듈이 저수준에 의존)

## Key Insights
- v1.3.0 기준 82개 파일, ~518K 단어의 중형 프로젝트. 1656개 노드의 지식 그래프가 추출될 만큼 구조화 수준이 높음
- `extract()` 함수가 80개 에지로 전체 시스템의 60개 이상 파라미터를 단일 진입점으로 수렴 — API 디자인이 매우 집중적
- LCS(Longest Common Subsequence) 기반 fuzzy alignment가 핵심 기술적 차별점. `resolver.py`가 1192줄로 가장 큰 파일
- Provider plugin 시스템이 잘 설계됨: entry-points + CLI scaffolding + pip 배포까지 지원
- `prompt_validation` 단계에서 extraction text가 example text의 verbatim인지 자동 검증 → 잘못된 few-shot 방지
- Gemini controlled generation (structured output) + code fence 자동 감지로 provider 간 호환성 유지
- 커뮤니티는 191개지만, cohesion ≥ 0.05인 의미 있는 커뮤니티는 12개에 불과 — 나머지는 대부분 개별 테스트 함수의 노이즈
- `_compat/` 모듈에서 backward compatibility shim을 통해 deprecation을 점진적으로 관리

## Related Entities
- [[high-performance-git]] — 또 다른 Google 오픈소스 툴, Git 성능 분석
- LLM Wiki ⚠️ 페이지 미생성 — LLM 기반 지식 관리 시스템

## See Also
- [COMMUNITY_PROVIDERS.md](https://github.com/google/langextract/blob/main/COMMUNITY_PROVIDERS.md)
- [RadExtract — Radiology Report Structuring](https://github.com/google/langextract#radiology-report-structuring-radextract)
- [Medication Extraction Example](https://github.com/google/langextract#medication-extraction)