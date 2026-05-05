# 연구 진행 보고 — 2026-05-02

## 이번 주 주요 발견

### 신규 논문 (8편 High, 2편 Medium)

#### 🔴 HIGH — 8편

1. **2602.23905** — *Novice Developers Produce Larger Review Overhead for Project Maintainers while Vibe Coding*
   - Asdaque, Haider, Malik (2026-02-27)
   - **최초의 vibe coding PR 실증 연구**: 22,953개 PR, 1,719명 vibe coder 분석
   - **핵심 결과**: 저경력 vibe coders는 4.52× 더 많은 리뷰 코멘트 유발, 31% 낮은 수락률, 5.16× 더 오래 열려있음
   - **의미**: developer experience는 AI-assisted 환경에서도 여전히 결정적 변수. "코드 생성량 ↑, 검증 부담 → 리뷰어 전가" 패턴

2. **2511.20613** — *Can Vibe Coding Beat Graduate CS Students? LLM vs. Human Coding Tournament*
   - Danassis, Goel (2025-11-25)
   - **LLM vs 인간 토너먼트**: 단순 unit-test 통과율 넘어 전략적 계획 평가
   - **의미**: Plan-First 원칙의 학술적 검증

3. **2604.16753** — *Know When to Trust the Skill: Delayed Appraisal and Epistemic Vigilance for Single-Agent LLMs*
   - Unlu (2026-04-17)
   - **Epistemic Vigilance**: AI agent가 자신의 skill/tool 출력을 지연 평가하여 context pollution 방지
   - **의미**: Awareness 계층의 agent-internal 구현 메커니즘

4. **2603.19461** — *HyperAgents*
   - Zhang, Zhao, Yang (2026-03-19)
   - **Self-improving AI 시스템**: Darwinian meta-learning, fixed handcrafted meta-mechanisms의 한계 극복
   - **의미**: Meta-Evolution 및 Research Agent 설계의 이론적 근거

5. **2603.03414** — *Cognitive Dark Matter: Measuring What AI Misses*
   - Mineault, Griffiths, Escola (2026-03-03)
   - **Cognitive Dark Matter**: 행동으로 추론 불가능한 뇌 기능(메타인지, 인지적 유연성). Jagged Intelligence의 원인
   - **의미**: Awareness 계층이 다루는 영역의 이론적 설명 제공

6. **2405.17739** — *The Widening Gap: Benefits and Harms of GenAI for Novice Programmers*
   - Prather, Reeves, Leinonen (2024-05-28)
   - **21회 lab session** — eye tracking + 인터뷰. 초보자의 GenAI 사용 격차 분석
   - **핵심 결과**: "illusion of competence" — 실제보다 자신의 능력을 과대평가. 메타인지 어려움이 GenAI로 악화됨
   - **의미**: Invisible Failure의 가장 강력한 경험적 증거. Accelerated vs. Struggling 그룹 간 격차 확대

7. **2604.19827** — *More Is Different: Toward a Theory of Emergence in AI-Native Software Ecosystems*
   - Russo (2026-04-20)
   - **복잡적응계(CAS) 관점**: comprehension debt, cascade failures 등이 개별 컴포넌트가 아닌 상호작용에서 발생
   - **의미**: 기존 comprehension debt 논의를 거시적 생태계 프레임워크로 확장

8. **2512.08942** — *Beyond Technical Debt: How AI-Assisted Development Creates Comprehension Debt in Resource-Constrained Indie Teams*
   - Zhang (2025-10-30)
   - **Comprehension Debt의 원천 논문**: 3인 distributed team autoethnography — AI가 만든 복잡한 시스템을 팀이 이해하지 못하는 패러독스
   - **의미**: Ahmad(2604.13277)가 인용한 선행 연구. Comprehension Debt → AI Dependency trap

#### 🟡 MEDIUM — 2편

9. **2509.03171** — *Plan More, Debug Less: Applying Metacognitive Theory to AI-Assisted Programming Education*
   - Phung, Choi, Wu (2025-09-03)
   - 102명 학생 대상 메타인지 힌트 시스템 실험 — planning hint가 debugging hint보다 높은 성적과 연결
   - **의미**: Plan-First 원칙의 실증적 검증

10. **2506.13845** — *Students' Reliance on AI in Higher Education: Identifying Contributing Factors*
    - Pitts, Rani, Mildort (2025-06-16)
    - Appropriate reliance vs. overreliance vs. underreliance 분류 + self-efficacy/literacy/need for cognition과의 상관관계
    - **의미**: RelianceScope(의존도 평가 도구)와 연결

### 이론적 프레임워크 업데이트

#### 새로운 개념 추가: Epistemic Vigilance + Cognitive Dark Matter

기존 4계층(HOW-WHAT-CARE-AWARENESS)의 Awareness 계층 확장:

| 차원 | 개념 | 출처 논문 |
|------|------|-----------|
| **Epistemic Vigilance** | AI 출력을 지연 평가하는 메타인지적 경계 | Unlu (2604.16753) |
| **Invisible Failure** | illusion of competence, metacognitive difficulty | Prather et al. (2405.17739) |
| **Epistemic Debt** | AI에 의존하며 쌓이는 이해 부재 | Sankaranarayanan (2602.20206) |
| **Deferred AI** | AI 도움을 의도적 지연 → 메타인지 촉진 | Kim et al. (2604.19931) |
| **Cognitive Dark Matter** | 행동으로 추론 불가능한 인지 기능 | Mineault et al. (2603.03414) |

#### Comprehension Debt의 진화

- **v1** (Zhang 2512.08942): AI가 만든 시스템을 팀이 이해 못함
- **v2** (Ahmad 2604.13277): AI-native workflows의 comprehension 부채
- **v3** (Russo 2604.19827): 복잡적응계(CAS) 관점의 emergence — 개별 agent가 아닌 상호작용에서 발생

## 현재 상태

### 연구 진행도

```
Phase 1: Initial Literature Review  ████████░░ 80% (4/26→4/30, 31 papers)
Phase 2: Deep Dive Analysis         ████░░░░░░ 40% (5/01→5/02, 10 papers)
Phase 3: Framework Development      ███░░░░░░░ 30%
Phase 4: Validation & Iteration     ░░░░░░░░░░  0%
```

### 논문 누적 통계

| 날짜 | High | Medium | 누계 |
|------|------|--------|------|
| 2026-04-26 | 12 | 0 | 12 |
| 2026-04-29 | 8 | 0 | 20 |
| 2026-04-30 | 11 | 2 | 33 |
| 2026-05-01 | 4 | 0 | 37 |
| **2026-05-02** | **8** | **2** | **47** |

### 이론적 기반 성숙도

- ✅ Epistemic Debt (Sankaranarayanan) — core framework
- ✅ Metacognitive Script Design (4종 사고 유도 질문)
- ✅ Deferred AI Assistance (delay → metacognition)
- ✅ Affective Safety 차원 (Empathic IDE/Ceci)
- ✅ Invisible Failure 패턴 (Widening Gap + illusion of competence)
- ✅ Comprehension Debt (3단계 진화)
- 🆕 Epistemic Vigilance (agent internal monitoring)
- 🆕 Cognitive Dark Matter (jagged intelligence theory)
- 🆕 Empirical Evidence (22,953 PRs vibe coding analysis)
- 🟡 Reliance Scope Metrics (적절/과잉/과소 의존 측정)

## 다음 단계

1. **Deep Dive — The Widening Gap (2405.17739)**
   - Prather et al.의 eye tracking 데이터 심층 분석
   - Accelerated vs. Struggling 그룹의 행동 패턴 → 메타인지 스크립트 설계에 반영
   - Illusion of competence 탐지 지표 개발

2. **Deep Dive — Epistemic Vigilance (2604.16753)**
   - Delayed Appraisal 메커니즘의 구체적 구현 방식 분석
   - Awareness 도구의 agent-internal 모니터링 설계에 적용

3. **Deep Dive — Cognitive Dark Matter (2603.03414)**
   - CDM이 측정하는 인지 기능 목록 → Awareness 계층의 측정 지표로 변환
   - Process-tracing data (eye-tracking, think-aloud)의 CDM 측정 방법론

4. **NotebookLM Pipeline 복구**
   - NotebookLM 인증 문제 해결 (nlwflow note-wiki로 10편 동시 분석)
   - Epistemic Vigilance + Cognitive Dark Matter + Comprehension Debt 3.0 synthesis

5. **Framework v2.0 준비**
   - HOW-WHAT-CARE-AWARENESS 4계층에 Epistemic Vigilance + CDM 통합
   - 초보자 진단 도구 설계 착수 (RelianceScope + Invisible Failure detection)

## 참고 자료

- `raw/research-reports/2026-04-26-research-progress.md` — Phase 1 시작 (12 papers)
- `raw/research-reports/2026-04-29-research-progress.md` — NotebookLM 분석 결과
- `raw/research-reports/2026-04-30-research-progress.md` — Epistemic Debt 발견 (11 High + 2 Medium)
- `raw/research-reports/2026-05-01-research-progress.md` — RelianceScope + Comprehension Debt 2.0
- `concepts/vibe-coding-beginner-methodology.md` — 종합 가이드
- arxiv links: 위 각 논문의 arxiv ID 참조