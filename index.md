# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-05-05 | Total pages: 68

## Concepts (23)
- [[agentic-ai-engineering]] — Agentic AI 엔지니어링: 3계층 아키텍처(Foundation→Agent→Production), framework 선택, memory, 평가 원칙
- [[anthropic-claude-think-tool]] — Anthropic의 "think" tool: 복잡한 도구 사용 상황에서 Claude가 멈추고 생각하게 하는 메커니즘
- [[claude-code-harness-plugin]] — Claude Code 하네스 플러그인: Parts 1~8의 추상 + 4 프리미티브(Subagent/Skill/Hook/Worktree) 구현체
- [[delegation-levels]] — 위임 레벨: 하네스 성숙도 + 영속적 HITL 축으로 L1~L4 결정, 프로젝트 내 차등 적용
- [[evaluator-role]] — Evaluator 역할: Layer 1~3 3중 평가 체계, Rubric 기반 자동화된 품질 게이트
- [[failure-modes]] — 실패 모드와 회복: 경계 침범·레벨 불일치 2축, 6카테고리, 4단계 회복 패턴
- [[generator-role]] — Generator 역할: plan.md → 구현 6단계 완료 체인(코드→테스트→CI→배포→헬스)
- [[git-agent-loop]] — Agent Loop 시대의 Git 워크플로우: worktrees 격리, maintenance 자동화, notes/trailers, range-diff 리뷰
- [[git-maintenance-strategy]] — git maintenance > git gc: commit-graph, Bloom filter, MIDX, bitmaps incremental 전략
- [[git-performance-diagnosis]] — Git 병목 진단: 5가지 분류(A~E), GIT_TRACE2 기반 레이어 식별
- [[harness-engineering]] — Harness Engineering: AI 에이전트 행동을 예측/통제 가능하게 만드는 소프트웨어 엔지니어링 분야
- [[hermes-15-hidden-features]] — Hermes Agent의 잘 알려지지 않은 15가지 기능 (Sharbel, 2026-04-28)
- [[knowledge-graph-code-intelligence]] — Knowledge Graph as Code Intelligence: 정적 분석 결과를 지식 그래프로 변환하는 새로운 코드 인텔리전스 패러다임
- [[large-repository-git-strategy]] — 대형 리포지토리 4차원: history depth, working tree width, blob weight, ref bloat
- [[llm-tdd-quality]] — LLM의 TDD 수행 품질: AI는 TDD를 제대로 할까? 회피 패턴·연구·대응 전략
- [[mediums-framework]] — 매개체 체계: Planner·Generator·Evaluator 3역할을 잇는 7개 문서(Constitution→Feature Spec→Sprint Contract→ADR)
- [[mcp-프로토콜]] — MCP (Model Context Protocol): AI 모델과 외부 도구/데이터소스 간 표준 통신 프로토콜
- [[planner-role]] — Planner 역할: 미션 정렬, Feature Spec과 Sprint Contract 작성, 구현 방법은 절대 기술하지 않음
- [[spec-driven-development]] — Spec-Driven Development: 코드 전에 명세 합의 — Delta Specs, OPSX 워크플로우, AI 협업 패턴
- [[vibe-coding-beginner-methodology]] — 바이브 코딩 초보자 방법론: 5단계 워크플로우·3계층 프롬프트·보안·한계 종합 가이드
- [[call-resolution-dag-scope-pipeline]] — Call-Resolution DAG vs Scope-Resolution Pipeline — GitNexus 언어 Provider의 두 호출 해석 패러다임
- [[vibe-coding-blueprint-pattern]] — Vibe Coding 블루프린트 패턴: 코드보다 설계를 먼저 생성하는 AI 협업 패턴
- [[바이브코딩-책-개요]] — 바이브코딩 책 (박경태, wikidocs) 전체 개요 및 구조
- [[에러-에스컬레이션]] — AI 코딩에서 에러가 발생했을 때 상위 지원으로 에스컬레이션하는 패턴
## Entities (39)
- [[agentic-ai-engineer-roadmap-2026-qa]] — 2026 Agentic AI Engineer 면접 Q&A (Lamhot Siagian): 11토픽 100문항, Python→Multi-Agent→Deploy
- [[animejs]] — Anime.js: JavaScript 애니메이션 엔진 (Julian Garnier). ES 모듈, Clock 기반 상속, CSS/SVG/JS/WAAPI 통합 API, 165개 소스파일
- [[app-blueprint-prompt]] — APP_BLUEPRINT.md 생성 메타프롬프트 (@DeRonin_ 제작)
- [[beads]] — gastownhall/beads: Dolt 기반 분산 그래프 이슈 트래커. AI 에이전트용 의존성 인식 작업 관리, MCP 서버 내장
- [[boop-agent]] — Boop Agent: iMessage 기반 오픈소스 개인 비서. Claude Agent SDK + Dispatcher-Worker 아키텍처
- [[cc-sdd]] — cc-sdd: Kiro 스타일 SDD — 8개 AI 에이전트 통합, Boundary-first 설계, 경량 npx 설치
- [[context-rot]] — Context Rot: LLM 대화가 길어질수록 컨텍스트 품질이 저하되는 현상과 대응 전략
- [[don-cheli-sdd]] — Don Cheli: Docker 기반 SDD 런타임 — 6단계 파이프라인, TDD 강제, 실패 시 worktree 폐기
- [[GitNexus]] — GitNexus (gitnexus/sdk): 코드베이스를 지식 그래프로 변환하는 Code Intelligence 플랫폼 — Tree-sitter AST 추출, LanguageProvider 다형성, MCP 표준 지원
- [[git-sync]] — git-sync: remote-to-remote Git 미러링 도구. Relay-first 설계, 인메모리 객체 저장소, smart HTTP 전용
- [[hands-on-large-language-models]] — Hands-On Large Language Models (Jay Alammar, Maarten Grootendorst): Illustrated LLM Book — 290+ 그림, 12 Colab 노트북, 9 보너스 가이드
- [[Qwen-FlashQLA]] — Qwen FlashQLA: TileLang 기반 고속 linear attention kernel (GDR forward 2-3×, backward 2× speedup)
- [[harness-engineering-wikidocs]] — 하네스 엔지니어링 입문 — 위키독스 요약
- [[high-performance-git]] — High Performance Git: Ted Nyman의 Git 내부·대형리포지토리 성능 최적화 (228p, CC BY-SA 4.0)
- [[LangExtract]] — Google HAI-DEF: LLM 기반 비정형 텍스트 구조화 추출. Few-shot, Source Grounding, Multi-Pass. Python ≥3.10
- [[stash]] — alash3al/stash: AI Agent용 8단계 Consolidation 기반 영구 기억 시스템. MCP·PostgreSQL·pgvector 기반
- [[Learn-Harness-Engineering]] — WalkingLabs: AI 코딩 에이전트를 위한 Harness Engineering 프로젝트 기반 과정 (12강의 + 6프로젝트)
- [[matt-pocock-caveman]] — 초압축 커뮤니케이션 모드. 토큰 사용량 ~75% 절감, 전체 기술 정확성 유지
- [[matt-pocock-grill-me]] — relentlessly 인터뷰로 설계 의사결정 트리 완전 해결 (비코드용)
- [[matt-pocock-grill-with-docs]] — grill-me + 공유 언어(CONTEXT.md) 구축 + ADR 기록 (코드용)
- [[matt-pocock-improve-codebase-architecture]] — 코드베이스 deepening 기회 발굴 (삭제 테스트, 모듈 깊이 분석)
- [[matt-pocock-skills]] — Matt Pocock의 Skills For Real Engineers: AI 에이전트용 실용 엔지니어링 스킬 모음
- [[matt-pocock-tdd]] — Red-Green-Refactor + 수직 슬라이스 TDD. 안티패턴 명시적 금지
- [[openspec]] — OpenSpec: Fission AI의 AI-native spec-driven 프레임워크 (TypeScript, 25+ AI 도구 지원, Graphify 분석)
- [[prompt-master]] — Prompt Master: 모든 AI 도구용 프롬프트 최적화 Claude 스킬 (30+ 프로필, 37 안티패턴) @nidhinjs
- [[subagent-driven-development]] — Superpowers의 핵심 패턴: 멀티 에이전트 병렬 구현 + Two-Stage Review
- [[superpowers]] — obra/superpowers: AI 코딩 에이전트 방법론 플러그인 (Jesse Vincent, Anthropic)
- [[warp]] — Warp: Rust 기반 Agentic Development Environment. 자체 UI 프레임워크, 내장 AI 에이전트, MCP 지원
- [[agent-rules-books]] — ciembor/agent-rules-books: 14개 클래식 SW 공학 서적 기반 AI 에이전트 규칙 (full/mini/nano 3 tier, Codex/Cursor/Claude Code agnostic)
- [[기억하는-개발]] — 기억하는 개발: 세션·컨텍스트·지식을 축적하며 발전하는 AI 협업 개발 방식
- [[엔지니어링-사고-초보자]] — 초보자를 위한 엔지니어링 사고: AI 협업에서 필요한 사고방식
- [[오케스트레이션]] — 오케스트레이션: AI 에이전트 간 작업 조율과 협업 패턴
- [[바이브 코딩이 실패하는 이유]] — 바이브코딩 책 챕터별 요약
- [[Engineering Mindset]] — Engineering Mindset 프로젝트 개요
- [[hermes-memory-architecture-analysis]] — Hermes Memory 구조 분석: Session, User, Memory 3-layer architecture
- [[obsidian-skills]] — kepano/obsidian-skills: Agent Skills for Obsidian — 마크다운, Base, CLI, Canvas, Defuddle 5종 스킬셋
- [[long-running-agents]] — Long-Running Agents: 교대(shift) 기반의 장기 실행 AI 에이전트 — fresh context windows로 Context Rot 극복, Ralph loop로 자체 품질 검증 (Addy Osmani, 2026)
- [[ProjectsMD]] — am423/ProjectsMD: 단일 파일 프로젝트 관리 CLI — Rust, project.md 포맷, 에이전트-인간 협업
- [[Scrapling]] — 탐지 불가능한 웹 스크래핑 라이브러리 (Python) — 3-tier Fetcher(정적→동적→스텔스) + Spider 프레임워크
- [[Memcord]] — MCP 기반 AI 에이전트 메모리 서버 — 28 tools, 4종 summarizer backend, privacy-first self-hosted
## Comparisons (3)
- [[don-cheli-vs-cc-sdd]] — Don Cheli vs cc-sdd: Docker 파이프라인 SDD vs Boundary-first Skills SDD
- [[hermes-vs-boop]] — Hermes Agent vs Boop Agent: 아키텍처·Memory·통합·안전장치 전방위 비교
- [[openspec-vs-superpowers]] — OpenSpec vs Superpowers: Spec-first vs Process-first AI 협업 프레임워크 전방위 비교

## Queries (1)
- [[vibe-coding-beginner-checklist]] — 바이브 코딩 초보자 실행 체크리스트 (시작→환경→프롬프트→검증→보안→배포→개선)

## Raw Sources
- `raw/articles/` — 원본 아티클 11편 (보관)
- `raw/articles/harness-engineering/` — 하네스 엔지니어링 wikidocs 책 (10 Parts, JSON + 개별 마크다운)
- `raw/articles/notebooklm-note-wiki-vibe-coding-beginner-methodology-report` — NotebookLM 바이브 코딩 연구 리포트
- `raw/papers/ai-novice-to-senior-research` — 초보자 AI 협업 논문 메타 분석
- `raw/articles/so-ainsight-hermes-agent-top10-github-repos` — Hermes Agent 활용 GitHub 레포 10선 트윗 요약
- `/Users/bricoleur/artifacts/7ebb8346-3fa1-4f7a-9809-baa38faca430/` — NotebookLM 원본 아티팩트

