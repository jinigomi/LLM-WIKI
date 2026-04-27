# Anthropic Harness Design vs. HyperAgents 비교 분석

**문서 상태**: 초안
**작성일**: 2026-04-27
**분석 대상**: Anthropic "Harness design for long-running application development" vs. HyperAgents Meta Agent 설계

---

## 1. 핵심 유사성: Generator + Evaluator 패턴

### Anthropic (2026-03-24)

```
Generator (Task Agent)     Evaluator (Meta Agent)
      ↓                          ↓
 코드/설계 생성    →    품질 평가 (자율 판단 ❌ → 외부 평가 ✅)
                           ↓
                     실패 → Generator에 수정 지시
                     성공 → 통과
```

**핵심 발견**: "When asked to evaluate work they've produced, agents tend to respond by confidently praising the work—even when, to a human observer, the quality is obviously mediocre."

→ **자기 평가bias** 해결: Generator와 Evaluator를 분리

### HyperAgents (나의 설계)

```
Task Agent                 Meta Agent
  ↓                          ↓
 결과물 생성    →    Senior 품질 기준 검증
                    + Metacognitive Check
                           ↓
                     실패 → 수정 지시
                     성공 → 통과 + 이해도 인증
```

→ Generator(Task Agent)와 Evaluator(Meta Agent) **분리 구조** 동일

---

## 2. 주요 차이점

| 측면 | Anthropic Harness | HyperAgents (나) |
|------|------------------|------------------|
| **목표** | Long-running agent 코딩 품질 향상 | 초보자→Senior 수준 결과물 |
| **Evaluator 기준** | Grading criteria (설계 원칙) | Senior Developer 체크리스트 |
| **Metacognitive Check** | 없음 | 있음 (초보자 이해 보장) |
| **Knowledge Archive** | Artifacts (세션 간引き継ぎ) | Archive (성공/실패 패턴 축적) |
| **Context 관리** | Context resets + structured handoff | Archive를 통한 지식 지속성 |
| **Self-evaluation 해결** | 별도 Evaluator 분리 | 별도 Meta Agent 분리 |

---

## 3. Anthropic의 핵심 인사이트 (내 설계에 반영)

### 3.1 자기 평가bias 문제

> "Separating the agent doing the work from the agent judging it proves to be a strong lever to address this issue."

**나의 설계 적용**: Task Agent (생성) ≠ Meta Agent (평가) — 완전히 분리

### 3.2 Grading Criteria의 중요성

> "Is this design beautiful? is hard to answer consistently, but 'does this follow our principles for good design?' gives Claude something concrete to grade against."

**나의 설계 적용**: Senior Developer 체크리스트 10개 항목 (추상적 "좋은 코드" → 구체적 체크리스트)

### 3.3 Evaluator의 "taste" 문제

> "The separation doesn't immediately eliminate that leniency on its own; the evaluator is still an LLM that is inclined to be generous towards LLM-generated outputs."

**나의 설계 적용**: Metacognitive Check 추가 — LLM Evaluator의 관대함을 초보자의 "이해도"로 보정

### 3.4 Context 관리

> "Context resets—clearing the context window entirely and starting a fresh agent, combined with a structured handoff that carries the previous agent's state and the next steps."

**나의 설계 적용**: Archive가 "structured artifact" 역할을 함 — 세션 간 지식引き継ぎ

---

## 4. Anthropic의 3-Agent Architecture

```
Planner          Generator           Evaluator
  ↓                 ↓                    ↓
작업 분해     코드/설계 생성      품질 평가 + 수정 지시
  ↓                 ↓                    ↓
          ←←←←←← iteration ←←←←←←←
```

**나의 설계와의 대응**:

| Anthropic | 나 | 역할 |
|-----------|-----|------|
| Planner | (설계에 내재) | 작업 분해 |
| Generator | Task Agent | 결과물 생성 |
| Evaluator | Meta Agent | 품질 평가 + 수정 지시 |

---

## 5. 내 설계의 추가 기여점 (Anthropic에 없는 것)

### 5.1 Metacognitive Check

Anthropic은 **품질 평가**에만 Evaluator를 사용.
나의 설계는 Evaluator가 두 가지 역할:
1. **품질 Review** (Anthropic과 동일)
2. **Metacognitive Check** (초보자 이해 확인) ← **Anthropic에 없는 inovation**

**이유**: 초보자의 결과물이 Senior 기준을 통과해도, 그 결과물을 **이해하지 못하면** 나중에 Epistemic Debt가 누적됨 (Sankaranarayanan, 2026)

### 5.2 초보자 관점의 의도

Anthropic은 **AI 에이전트의 자율성 향상**에 초점.
나의 설계는 **초보자의 역량 성장**에 초점.
→ 같은 Generator/Evaluator 패턴이라도 **목적이 다름**.

### 5.3 Archive의 학습 기능

Anthropic의 artifacts는 **세션引き継ぎ** 목적.
나의 Archive는 **지식 축적 + 다음 iteration 개선** 목적.
→ 주기적 Meta-Evolution 루프와 연결.

---

## 6. 통합 가능성

두 접근법은 **상호 보완적**:

```
         Planner (작업 분해)
               ↓
        Generator (Task Agent)
               ↓
    ┌──────────┴──────────┐
    ↓                     ↓
Anthropic-style      HyperAgent-style
Quality Review       Metacognitive Check
    ↓                     ↓
    └──────────┬──────────┘
               ↓
          Archive (지식 축적)
               ↓
      Meta-Evolution (방법론 업그레이드)
```

---

## 7. 결론

**Anthropic의 Harness Design이 내 HyperAgents 설계의 "기술적 검증" 역할을 함.**

-Generator/Evaluator 분리 패턴이 실제로 효과적임을 Anthropic이 입증
- 내 설계는 여기에 **Metacognitive Check**와 **초보자 관점**을 추가하여 보완
- 두 접근법의 핵심 메시지: **"자기 평가는 신뢰할 수 없다 — 외부 Evaluator가 필요하다"**

---

## 8. 참고

- **Anthropic 원문**: https://www.anthropic.com/engineering/harness-design-long-running-apps
- **관련**: frontend design skill, long-running coding agent harness (Anthropic 이전 작업)
- **GANs에서 영감**: Generator vs Discriminator → Generator vs Evaluator로 차용

---

*문서 버전: 1.0*
*생성일: 2026-04-27*