---
title: Research Agent 설계 계획
created: 2026-04-27
updated: 2026-04-27
type: entity
tags: [agent, research, orchestration, tool-use]
sources: []
confidence: high
---

# Research Agent 설계 계획

> **For Hermes:** writing-plans → subagent-driven-development 순서로 실행

**Goal:** 초보자가 Senior Developer 수준 결과물을 만들 수 있는 AI 협업 연구 에이전트 시스템 구축

**Architecture:** Multi-Agent Workflow (Researcher/Critic/Synthesizer) + 도구 통합 + 계층적 관리

---

## 1. 연구 에이전트 필수 도구 (Tools)

### 1.1 웹 검색 & 브라우징
| 도구 | 용도 | 우선순위 |
|------|------|---------|
| Tavily | AI 최적화 검색 API | High |
| Perplexity API | 실시간 웹 답변 | High |
| Firecrawl | 웹페이지 크롤링 | High |
| SerpAPI | Google 검색 | Medium |

### 1.2 학술 문헌 검색
| 도구 | 용도 | 우선순위 |
|------|------|---------|
| ArXiv API | 논문 검색 (이미 사용 중) | High |
| Semantic Scholar API | 학술 검색 | High |
| Elicit | 연구 paper 발견 | Medium |

### 1.3 코드 실행 & 실험
| 도구 | 용도 | 우선순위 |
|------|------|---------|
| Python REPL (execute_code) | 데이터 처리/분석 | High |
| Jupyter (jupyter-live-kernel) | 대화형 분석 | High |

### 1.4 파일/데이터 처리
| 도구 | 용도 | 우선순위 |
|------|------|---------|
| PDF 파싱 (ocr-and-documents) | 논문 읽기 | High |
| Obsidian (obsidian) | Wiki 연동 | High |

### 1.5 평가 도구
| 도구 | 용도 | 우선순위 |
|------|------|---------|
| LLM-as-a-Judge | 품질 평가 | Medium |
| Human-in-the-loop | 체크포인트 | High |

---

## 2. 주요 Agentic Design Patterns

### 2.1 Chain of Thought (CoT) + ReAct
- 생각 → 행동 → 관찰 반복
- 연구 과정에서 추론의 투명성 확보

### 2.2 Plan-and-Execute
1. 전체 계획 세움
2. 단계별 실행
3. 필요시 계획 수정

### 2.3 Multi-Agent Workflow
| 역할 | 책임 |
|------|------|
| **Researcher** | 문헌 조사, 데이터 수집 |
| **Critic** | 비판적 평가, 품질 검증 |
| **Synthesizer** | 지식 합성, 결론 도출 |
| **Writer** | 보고서 작성 |

### 2.4 Reflection / Self-Critique
- 결과물 스스로 평가
- hallucination 체크
- 수정 반복

### 2.5 Hierarchical Agents
- Supervisor Agent가 Sub-agents 지휘
- 작업 분배 및 결과 취합

---

## 3. 연구 워크플로우 (6단계)

```
단계 1: 연구 질문 해석 + 계획 수립
  ↓
단계 2: 문헌 조사 (Deep Research)
  ↓
단계 3: 지식 합성 + Gap 분석
  ↓
단계 4: 가설 생성 또는 실험 설계
  ↓
단계 5: 코드/시뮬레이션 실행
  ↓
단계 6: 결과 분석 + 보고서 작성 + 인용 관리
```

---

## 4. 구현 태스크

### Task 1: 현재 도구 현황审计
**Objective:** 현재 Hermes Agent의 도구能力を审计

**Files:**
-审计: `~/.hermes/skills/` (전체 스킬 목록)
-확인: 사용 가능한 MCP 서버 및 도구

### Task 2: 연구 에이전트 스킬 생성
**Objective:** 연구용 스킬 생성

**Files:**
- 생성: `skills/research/research-agent/SKILL.md`

**내용:**
- Multi-Agent Workflow 정의
- CoT + ReAct 패턴 통합
- 도구 연동 가이드

### Task 3: 연구 결과 저장 구조 확립
**Objective:** 위키에 연구 결과 저장 규칙 정의

**Files:**
- 생성: `docs/research-sop.md`
- 규칙: raw/ → entities/concepts/ 흐름

### Task 4: Cron 보고 최적화
**Objective:** 매일 오후 8시 보고 Cron 개선

**Files:**
- 수정: 기존 Cron job (job_id: 1276e4acc547)
- 추가:昨夜 연구 결과 요약 자동 생성

### Task 5: Human-in-the-Loop 체크포인트
**Objective:** 중요 결정 시 인간 검토 포인트 설정

**Files:**
- 생성: `docs/research-checkpoints.md`
- 정의: 언제 인간에게 확인받는가

---

## 5. 평가 기준

| 지표 | 측정 방법 |
|------|----------|
| 정확성 (Accuracy) | LLM-as-a-Judge 평가 |
| Hallucination Rate | 사실 확인 비율 |
| Groundedness | 출처 기반 비율 |
| 재현성 (Reproducibility) | 같은 질문에 다른 결과 |

---

## 6. 시작 가이드

1. **CrewAI**로 간단한 연구 팀 먼저 구성
2. 검증 후 필요 시 **LangGraph**로 마이그레이션

---

*Plan created: 2026-04-27*
*Status: Planning → Ready to Execute*