---
title: 'The "think" tool: Enabling Claude to stop and think in complex tool use situations'
source: Anthropic Engineering
date: 2025-03-20
url: https://www.anthropic.com/engineering/claude-think-tool
tags:
- reasoning
- inference
- tool-use
- model
- lab
created: '2026-04-25'
type: concept
updated: '2026-04-25'
confidence: high
---

# The "think" tool: Enabling Claude to stop and think

## 핵심 요약

도구 호출 중 **"think" 도구**를 통해 명시적 사고 공간 제공. 복잡한 작업에서 54% 성능 향상.

> ⚠️ **2025년 12월 업데이트**: Extended thinking이 개선되어 대부분의 경우 그것을 권장함. "think" 도구는 복잡한 도구 체인에서 여전히 유용.

## think 도구 vs Extended Thinking

| 구분 | Extended Thinking | think 도구 |
|------|-------------------|------------|
| **시점** | 응답 생성 **전** | 응답 생성 **도중** |
| **용도** | 계획 세우기, 반복적 사고 | 외부 정보 처리 후 판단 |
| **적합 상황** | 단순 도구 호출, 코딩/수학 | 복잡한 도구 체인, 정책 환경 |

## 구현 예시

```json
{
  "name": "think",
  "description": "Use the tool to think about something. It will not obtain new information or change the database, but just append the thought to the log.",
  "input_schema": {
    "type": "object",
    "properties": {
      "thought": {
        "type": "string",
        "description": "A thought to think about."
      }
    },
    "required": ["thought"]
  }
}
```

## τ-Bench 평가 결과 (항공권 도메인)

| 설정 | pass^1 | 개선幅度 |
|------|--------|----------|
| Baseline | 0.332 | - |
| Extended thinking | 0.412 | +24% |
| "think" 도구 | 0.404 | +22% |
| **"think" + 최적화 프롬프트** | **0.570** | **+54%** |

## 효과적인 프롬프트 예시

```markdown
## Using the think tool

Before taking any action or responding to the user after receiving tool results, 
use the think tool as a scratchpad to:
- List the specific rules that apply to the current request
- Check if all required information is collected
- Verify that the planned action complies with all policies
- Iterate over tool results for correctness
```

## 언제 think 도구를 사용하나?

✅ **효과적:**
- 도구 출력 분석 (이전 호출 결과를 주의 깊게 처리)
- 정책 무거운 환경 (상세 가이드라인 준수 필요)
- 순차적 의사결정 (각 단계가 이전에 기반, 실수가 비용 큼)

❌ **효과 없음:**
- 비순차적 도구 호출 (단일 또는 병렬 호출만 필요)
- 단순 지시 준수 (很多 제약이 없을 때)

## 출처

[The "think" tool: Enabling Claude to stop and think](https://www.anthropic.com/engineering/claude-think-tool) - Anthropic Engineering, 2025.03.20