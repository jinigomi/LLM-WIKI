---
title: Agentic AI Engineer Roadmap (2026) Interview Q&A
created: 2026-05-01
updated: 2026-05-01
type: entity
tags: [agentic-ai, ai-ml, education, agent-framework, skills, coding-agent, multi-agent, rag, evaluation]
sources: [raw/articles/agentic-ai-engineer-roadmap-2026-qa.md]
confidence: high
---

# Agentic AI Engineer Roadmap (2026) Interview Q&A

## Overview
Lamhot Siagian이 2026년 1월에 발표한 **Agentic AI 엔지니어 면접 준비 가이드**. 23페이지 분량으로, Python 기초부터 멀티 에이전트, 프로덕션 배포까지 11개 토픽, 토픽당 10개 질문 + 답변 + 코드 예제 포함. 2026년 Agentic AI 학습 로드맵을 인터뷰 실전 연습으로 전환한 문서.

- **저자:** Lamhot Siagian (PhD Student, AI Evaluation Engineer, Machine Learning)
- **일자:** January 19, 2026
- **분량:** 23 pages, 11 sections, ~100 Q&A
- **대상:** Agentic AI 엔지니어 지망생, 기술 면접 준비생
- **링크:** [Google Drive](https://drive.google.com/file/d/1JkNUDOztGVX7LLbMgkiOr0vffBLg8PeU/view)

## 구성 (11개 섹션)

| # | 섹션 | 핵심 질문 |
|---|------|----------|
| 1 | How to Use This Roadmap | 학습 순서·연습 방법 |
| 2 | Python Fundamentals | 타입 힌트·Pydantic·async · 프로젝트 구조 |
| 3 | LLM Fundamentals | 토큰·컨텍스트 윈도우·temperature·프롬프트 엔지니어링 |
| 4 | Framework 선택 | LangGraph vs CrewAI vs AutoGen 비교 |
| 5 | Advanced Framework | LCEL·Runnables·워크플로우·Multi-Agent |
| 6 | Memory Management | Short/Long-term·체크포인팅·임베딩 vs 요약 |
| 7 | Tool Integration | Custom tools·connectors·데코레이터·MCP |
| 8 | RAG Systems | 청크 전략·하이브리드 검색·재랭킹·평가 |
| 9 | Agents & Multi-Agents | ReAct·Supervisor·에이전트 간 통신·Human-in-the-loop |
| 10 | Production | FastAPI·Streamlit·Docker·AWS 배포 |
| 11 | Quick Checklist | 전체 커리큘럼 실행 체크리스트 |

## 핵심 인사이트

### Foundation-First 접근법
"core programming → LLM concepts → frameworks → advanced agent architecture → production deployment" 순서로 학습. 프레임워크를 먼저 배우는 건 안티패턴 — **아키텍처는 상태 모델, 도구 경계, 데이터 계약, 안전 규칙**이다.

### Production Agent의 핵심
- **State-first 설계**: typed, minimal state → 재현·관찰·안전성 확보
- **실패 분류**: transient(백오프 재시도) vs persistent(단순 도구 폴백/질문)
- **Router-Verifier 패턴**: Router(결정) → Tool Executor(실행) → Verifier(검증)
- **Fail-closed**: 스키마 검증 실패 시 graceful degradation, 잘못된 응답 금지

### Framework 선택 기준
- **LangGraph**: 명시적 상태 머신/그래프, 재시도, 장기 워크플로우, HITL(Human-in-the-loop)에 강점
- **CrewAI**: role-based 멀티 에이전트 협업, opinionated
- **AutoGen**: 에이전트 간 채팅 패턴, 유연성

### 기억(Memory) 전략
| 유형 | 저장 위치 | 용도 |
|------|----------|------|
| Short-term | 컨텍스트 윈도우 | 최근 턴, 도구 출력, 현재 태스크 |
| Long-term (요약) | DB·파일 | 결정·약속·선호도 |
| Long-term (임베딩) | Vector Store | 시맨틱 검색 (노트·문서·과거 티켓) |
| Checkpoint | Workflow 상태 | 장기 실행 복원·감사 |

### RAG 핵심 원칙
- 청크 크기: 300-800 토큰 + 10-20% 오버랩 (시작점)
- 하이브리드 검색(dense + sparse = BM25 + embedding)이 recall 향상
- 재랭킹(re-ranking)으로 정밀도 향상
- 평가 지표: Recall@k, Precision, MRR, Faithfulness

## 연결 개념
- [[spec-driven-development]] — Agent 아키텍처에서 state/contract 중심 설계의 연장선
- [[오케스트레이션]] — Supervisor agent 패턴과 직접 연결
- [[subagent-driven-development]] — Multi-Agent 시스템의 실전 구현 패턴
- [[에러-에스컬레이션]] — 실패 분류(transient/persistent)와 대응 전략
- [[mcp-프로토콜]] — Tool Integration의 표준화된 방식
- [[llm-tdd-quality]] — AI 평가·품질 측정 방법론
- [[openspec]] — AI-native spec-driven 프레임워크 (LangGraph 대안 분석)
- [[high-performance-git]] — 프로덕션 배포 섹션의 Docker·CI/CD 연관
- mihomo-rust-claude-code-porting ⚠️ 페이지 미생성 — Agent Team 실전 사례: 4역할, 40spec, 619테스트

## 주목할 점
- **"프레임워크는 도구, 아키텍처는 설계"** 라는 메시지가 일관되게 강조됨
- 90개 체크리스트(섹션 11)가 실용적: 질문 읽기 → 자기 말로 재작성 → 작은 프로젝트 구현 → 실패·수정 노트
- 평가(Evaluation)가 누락된 면접 답변은 감점. Golden set 구축, A/B 테스트, 휴먼 리뷰를 강조
- 면접관이 듣고 싶어하는 답변: "작게 시작해서 레이어를 단단하게(harden layers)"