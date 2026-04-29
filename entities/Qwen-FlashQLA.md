---
title: Qwen FlashQLA
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [entity, llm-inference, linear-attention, kernel, tilelang, qwen, cuda, hopper, gdn]
confidence: high
source: https://github.com/QwenLM/FlashQLA
source-type: github-repo
analyzed-by: graphify-v2
---

# Qwen FlashQLA

Qwen 팀의 고성능 linear attention kernel 라이브러리. [TileLang](https://github.com/tile-ai/tilelang) 기반.

## 핵심 요약

- **Target**: GDN (Gated DeltaNet) Chunked Prefill의 forward/backward 최적화
- **성능**: FLA Triton kernel 대비 forward 2-3×, backward 2× speedup
- **효과적 영역**: pretraining + 엣지사이드 agentic inference
- **요구사항**: SM90+ (Hopper), CUDA 12.8+, PyTorch 2.8+

## 3대 핵심 기술

### 1. Gate-driven automatic intra-card Context Parallelism
GDN gate의 지수적 감쇄 특성을 활용해 TP + long sequence + small head count 상황에서 자동 intra-card CP 활성화 → GPU SM utilization 개선

### 2. Hardware-friendly algebraic reformulation
Forward/backward flow를 수치 정밀도 손실 없이 재구성 → Tensor Core, CUDA Core, SFU 오버헤드 감소

### 3. TileLang fused warp-specialized kernels
전체 fused kernel도, step-by-step 분해도 아닌 중간 전략:
- key fused kernels를 TileLang으로 구축
- 수동 warpgroup specialization → data movement / Tensor Core / CUDA Core computation overlap

## Graphify 분석

- **91 nodes · 176 edges · 10 communities** (23 files, ~19K words)
- **추출**: 67% 추출, 33% 추론 (edges) — 소형 레포에 AST 기반 추출
- **신뢰도**: 추론 엣지 평균 confidence 0.8

### God Nodes (중심 추상화)
| Rank | Node | Edges |
|------|------|-------|
| 1 | `pack()` | 12 |
| 2 | `unpack()` | 11 |
| 3 | `pad_and_reshape()` | 11 |
| 4 | `prepare_chunk_offsets()` | 9 |
| 5 | `torch_chunk_gdr_fwd()` | 8 |
| 6 | `chunk_gated_delta_rule_bwd()` | 8 |

### 핵심 커뮤니티

| Community | Cohesion | Nodes | 설명 |
|-----------|----------|-------|------|
| 0 | 0.14 | 15 | fused GDR + TileLang 통합 커널 + chunk index/offset prep |
| 1 | 0.37 | 17 | GDR chunk forward/backward + pack/unpack/pad (핵심 텐서 변환) |
| 2 | 0.23 | 13 | benchmark suite + library version 수집 |
| 3 | 0.23 | 9 | chunk local cumsum + group reduce (TileLang + PyTorch) |
| 4 | 0.31 | 8 | intra-card CP preprocess + warmup + correct initial states |
| 5 | 0.28 | 5 | high-level `chunk_gated_delta_rule()` + `l2norm` + test harness |

### Cross-community bridges (high betweenness)
- `l2norm()`: Community 5 ↔ Community 2 (betweenness 0.312)
- `prepare_chunk_offsets()`: Community 0 ↔ Community 1 (betweenness 0.262)
- `prepare_tensors()`: Community 2 ↔ Community 5 (betweenness 0.252)

### Knowledge Gaps
- 10 isolated nodes (ModelConfig, benchmark wrappers 등) — docstring 부족
- `setup.py`, 3개의 `__init__.py`가 각자 thin community — 정상, 패키지 구조의 artifact

## 코드 아키텍처

```
flash_qla/
├── __init__.py           # high-level API: chunk_gated_delta_rule()
├── ops/
│   ├── chunk_gdr.py     # GDR forward/backward 구현
│   └── tilelang_gdr.py  # TileLang fused kernel 정의
├── utils/
│   ├── math.py          # l2norm 등 수치 연산
│   ├── index.py         # chunk offset 계산
│   └── packing.py       # pack/unpack/pad_and_reshape
├── cp/                  # Context Parallelism 관련
└── benchmark/           # 성능 측정
tests/
├── test_gdr.py          # 정확도 테스트
└── ref_gdr.py           # PyTorch reference 구현
```

## API

### High-level
```python
from flash_qla import chunk_gated_delta_rule
o, final_state = chunk_gated_delta_rule(q, k, v, g, beta, scale=scale,
    initial_state=None, output_final_state=True, cu_seqlens=None)
```

### Low-level
```python
from flash_qla import chunk_gated_delta_rule_fwd, chunk_gated_delta_rule_bwd
g, A, o, h, final_state = chunk_gated_delta_rule_fwd(...)
dq, dk, dv, db, dg, dh0 = chunk_gated_delta_rule_bwd(...)
```

## 관련 링크

- [[TileLang]] - 기반 커널 DSL
- [[FlashLinearAttention]] - 비교 대상 (FLA Triton kernel)
- [[GDN-Gated-DeltaNet]] - underlying architecture