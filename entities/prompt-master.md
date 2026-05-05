---
title: Prompt Master (Claude Skill)
created: 2026-04-30
updated: 2026-04-30
type: entity
tags: [prompt-engineering, claude-skill, agent-framework, optimization]
sources: [https://github.com/nidhinjs/prompt-master]
confidence: high
---

## 개요

Prompt Master는 **모든 AI 도구를 위한 프롬프트 최적화** Claude 스킬이다. 사용자의 rough idea를 받아 대상 AI 도구를 식별하고, 의도를 추출한 뒤, **단일 복사-붙여넣기 가능한 프로덕션 프롬프트**를 출력한다. "모든 단어가 load-bearing"이라는 철학 아래, 토큰 낭비 없는 날카로운 프롬프트를 만드는 것이 목표.

- **저자:** @nidhinjs
- **버전:** 1.6.0
- **라이선스:** MIT
- **타입:** Claude Skill (Markdown)

## 핵심 설계 철학

> "The best prompt is not the longest. It's the one where every word is load-bearing."

Prompt Master는 "긴 프롬프트 = 좋은 프롬프트"라는 통념을 깨고, 모든 문장이 결과물에 기여하는 **토큰 효율성**을 최우선으로 한다. 프레임워크 이름은 절대 노출하지 않고, 오직 결과물만 전달하는 **silent routing** 방식.

## 아키텍처: PAC2026 (30/55/15)

SKILL.md는 **PAC2026 attention architecture**를 따른다:

| Zone | 비중 | 내용 |
|------|------|------|
| **Primacy (상단 30%)** | 30% | Identity, Hard Rules, Output Format — 가장 먼저 읽히는 영역에 핵심 제약 배치 |
| **Middle (중간 55%)** | 55% | Intent Extraction (9차원), Tool Routing (30+ 도구), Diagnostic Checklist |
| **Recency (하단 15%)** | 15% | Verification checklist, Success criteria — 마지막으로 읽히는 영역에 최종 검증 |

## 작동 파이프라인 (7단계)

1. **Target Tool Detection** — 어떤 AI 도구인지 식별, 자동 라우팅
2. **9-Dimension Intent Extraction** — Task, Target Tool, Output Format, Constraints, Input, Context, Audience, Success Criteria, Examples
3. **Clarifying Questions** — 최대 3개까지 critical info 질문
4. **Template Routing** — 13개 템플릿 중 자동 선택
5. **Safe Techniques Only** — Role Assignment, Few-Shot, XML Tags, Grounding Anchors, CoT (o3/R1 등 reasoning 모델 제외)
6. **Token Efficiency Audit** — 결과물에 기여하지 않는 모든 단어 제거
7. **Prompt Delivery** — 단일 복사 가능 블록 + 1줄 전략 노트

## 지원 도구 (30+)

### Reasoning LLM
Claude, ChatGPT/GPT-5.x, Gemini 2.x/3 Pro, o3/o4-mini, Ollama, Qwen 2.5/3, Llama/Mistral, DeepSeek-R1, MiniMax M2.7/M2.5

### Agentic AI
Claude Code, Cursor/Windsurf, Cline, GitHub Copilot, Antigravity, Bolt/v0/Lovable/Figma Make/Google Stitch, Devin/SWE-agent

### Computer-Use/Browser Agents
Perplexity Comet/Computer, OpenAI Atlas, Claude in Chrome, OpenClaw

### Image/3D/Video/Voice AI
Midjourney, DALL-E 3, Stable Diffusion, SeeDream, ComfyUI, Meshy/Tripo/Rodin, Unity AI/BlenderGPT, Sora/Runway/Kling/LTX/Dream Machine, ElevenLabs

### Research/Workflow
Perplexity, Manus AI, Zapier/Make/n8n

## 13개 프롬프트 템플릿 (자동 선택)

| 템플릿 | 대상 |
|--------|------|
| RTF (Role, Task, Format) | 빠른 one-shot 작업 |
| CO-STAR | 전문 문서, 비즈니스 라이팅 |
| RISEN | 복잡한 멀티스텝 프로젝트 |
| CRISPE | 창의적 작업, 브랜드 보이스 |
| Chain of Thought | 논리, 수학, 디버깅 |
| Few-Shot | 일관된 구조 출력 |
| File-Scope | Cursor, Windsurf, Copilot |
| ReAct + Stop Conditions | Claude Code, Devin 등 자율 에이전트 |
| Visual Descriptor | Midjourney, DALL-E, SD, Sora |
| Reference Image Editing | 기존 이미지 편집 |
| ComfyUI | 노드 기반 이미지 워크플로우 |
| Prompt Decompiler | 기존 프롬프트 분해/적응 |
| Opus 4.7 Task Brief | Opus 4.7 복잡/멀티스텝 작업 |

## 37가지 Credit-Killing 패턴 감지

Prompt Master는 사용자 입력에서 37가지 패턴을 진단한다:
- **Task (7):** vague verb, two tasks in one, no success criteria, over-permissive agent, emotional description, build-the-whole-thing, implicit reference
- **Context (6):** assumed prior knowledge, no project context, forgotten stack, hallucination invite, undefined audience, no mention of prior failures
- **Format (6):** missing output format, implicit length, no role assignment, vague aesthetic adjectives, no negative prompts, prose for Midjourney
- **Scope (6):** no scope boundary, no stack constraints, no stop condition, no file path, wrong template, pasting entire codebase
- **Reasoning (5):** no CoT for logic, CoT on reasoning models, no self-check, expecting inter-session memory, contradicting prior decisions
- **Agentic (5):** no starting state, no target state, silent agent, unlocked filesystem, no human review trigger

## 제외된 기법 (의도적)

다음 기법들은 **단일 프롬프트 실행 시 fabrication을 유발**하므로 명시적으로 제외:
- Mixture of Experts (실제 라우팅 없이 role-play)
- Tree of Thought (선형 텍스트로 시뮬레이션, 실제 병렬성 없음)
- Graph of Thought (외부 그래프 엔진 필요)
- Universal Self-Consistency (독립 샘플링 필요, contamination 발생)
- Prompt Chaining as layered technique (긴 체인에서 fabrication)

## 강점

- **30+ 도구 전용 프로필** — 각 도구의 특이성(Claude Opus 4.7의 literalism, o3의 CoT 회피 등)에 맞춘 최적화
- **PAC2026 attention architecture** — 상단 30%에 핵심 제약을 배치해 attention decay 방지
- **Silent routing** — 프레임워크 이름 노출 없이 결과물만 전달
- **Memory Block 시스템** — 세션 히스토리를 첫 30%에 주입해 맥락 유지
- **35+ 안티패턴 진단** — 잘못된 프롬프트를 입력받아도 자동 수정

## 약점

- **Claude Skill 전용** — Claude 외 다른 에이전트에서는 SKILL.md를 직접 활용할 수 없음
- **긴 SKILL.md (422줄)** — 전체 로딩 시 토큰 비용 높음
- **도구별 지식의 정확성 의존** — Claude Opus 4.7, o3 등 빠르게 진화하는 모델의 특성이 outdated 될 위험
- **참조 파일 분리로 인한 지연** — templates.md(452줄), patterns.md(82줄)을 별도 로딩해야 함

## 연결 개념

- prompt engineering — 프롬프트 엔지니어링의 기본 개념
- Claude Code — 주요 타겟 도구 중 하나
- agent frameworks — SDD/TDD와 마찬가지로 AI 에이전트 제어를 위한 프레임워크