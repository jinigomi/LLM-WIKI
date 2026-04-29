# 연구 진행 보고 — 2026-04-29

## 이번 주 주요 발견

### 1. 🔥 핵심 논문 발견: "A Paradox of AI Fluency" (Potts & Sudhof, 2026-04-28)
- **arxiv: 2604.25905v1** — WildChat-4.8M의 27K 트랜스크립트 분석
- **핵심 주장**: AI에 능숙한 사용자(fluent)는 초보자(novice)보다 **더 많은 실패**를 경험하지만, 그 실패는 가시적(visible)이고 부분 회복(partial recovery) 가능. 초보자는 "보이지 않는 실패(invisible failure)"를 더 많이 경험 — 대화가 성공적으로 끝난 것처럼 보이지만 실제로는 목표를 달성하지 못함.
- **패러독스**: 능숙한 사용자는 더 복잡한 작업에 도전하고, AI와 **반복적으로 협업**하며 목표를 정제하고 출력을 비판적으로 평가. 초보자는 **수동적 자세**를 취함.
- **시사점**: 
  - 초보자 AI 협업의 가장 큰 문제는 "자신이 실패했는지조차 모른다"는 것
  - 능숙한 사용자의 "적극적 개입(active engagement)" 패턴을 초보자에게 가르치는 것이 핵심
  - AI 제품 설계자는 "마찰 없는 경험"보다 "깊은 참여를 유도하는 경험"을 설계해야 함
  - 연구의 **"초보자 Senior-level AI 협업 방법론"** 과제와 직접적 연관 — 초보자가 fluent user의 상호작용 패턴을 학습하는 커리큘럼 필요성 입증

### 2. Recursive Multi-Agent Systems (Yang et al., 2026-04-28)
- **arxiv: 2604.25917v1** — 멀티 에이전트 협업을 재귀적 계산으로 확장
- RecursiveMAS: 이종 에이전트 간 latent-state 협업 루프, 9개 벤치마크 평균 8.3% 정확도 향상
- 시사점: 초보자를 위한 multi-agent 협업 패턴에 `recursive refinement` 추가 고려 — 단일 에이전트 평가 대신 재귀적 교차 검증

### 3. Superpowers GitHub 레포 심층 분석 완료 (2026-04-29)
- obra/superpowers v5.0.7 전체 분석: Graphify AST + 15개 SKILL.md 수동 분석
- **핵심 패턴 수집**:
  - `subagent-driven-development` — 신선한 서브에이전트 + Two-Stage Review(명세 준수 → 코드 품질)
  - `systematic-debugging` — 4단계 근본 원인 디버깅 (버그 이해 → 근본 원인 → 수정 → 회귀 테스트)
  - `requesting-code-review` — 커밋 전 보안 스캔 + 품질 게이트
  - `test-driven-development` — RED-GREEN-REFACTOR 강제, 테스트 우선
  - `writing-plans` — bite-sized 태스크로 구현 계획 수립
- **위키 반영**: `entities/superpowers.md`, `entities/subagent-driven-development.md`

### 4. Wiki 정리 및 품질 관리 (2026-04-29)
- **Lint 수행**: 27개 페이지, 34개 깨진 wikilink 수정, 20개 인덱스 누락 복구, 7개 프론트매터 이슈 해결
- **불필요 페이지 5개 삭제**: 중복 비교/쿼리 페이지, 세션 로그, 개인 TODO, Discord 채널 메모
- **Orphan 해소**: 9개 오펀 중 8개 inbound wikilink 추가로 해결 (1개 허용)

## 현재 상태

| 영역 | 상태 | 비고 |
|------|------|------|
| 초보자→시니어 AI 협업 12개 논문 리서치 | ✅ 완료 (2026-04-26) | `raw/papers/ai-novice-to-senior-research.md` |
| Vibe Coding 초보자 방법론 | ✅ 완료 (2026-04-28) | 3개 위키 페이지 + NotebookLM 분석 |
| Vibe Coding 연구 논문 수집 | ✅ 완료 (2026-04-28) | 6편, `raw/papers/` |
| Research Agent 설계 | ✅ 완료 (2026-04-27) | 5개 구현 태스크 정의됨 |
| Research Agent 구현 | ⏳ 대기 중 | writing-plans → subagent-driven-development |
| Superpowers 분석 | ✅ 완료 (2026-04-29) | 2개 entity 페이지 |
| Wiki 품질 관리 | ✅ 완료 (2026-04-29) | Lint + 불필요 페이지 정리 |
| **신규 논문** "Paradox of AI Fluency" | ✅ 발견 (2026-04-29) | 연구 과제 직접 연관, Wiki 저장 필요 |
| **신규 논문** "RecursiveMAS" | ✅ 발견 (2026-04-29) | Multi-agent 패턴 참고 |
| **스킬 체계** | | |
| refinement-methodology | v1.3.2 | 3계층 품질 프레임워크 (How-What-Care) |
| meta-evolution-pattern | v1.3.2 | Blueprint Pattern, Multi-Perspective Evaluation |

## 다음 단계

1. **"Paradox of AI Fluency" 논문 심층 분석**: 
   - `raw/papers/2604.25905-paradox-ai-fluency.md`에 저장
   - `concepts/ai-fluency-paradox.md` 개념 페이지 작성
   - 초보자→fluent user 전환을 위한 구체적 행동 패턴 추출
   
2. **초보자 AI 협업 학습 경로 설계** (지연 중 — 최우선):
   - "Paradox of AI Fluency"의 fluent user 행동 패턴을 교육 가능한 커리큘럼으로 변환
   - vibe-coding-beginner-methodology + fluency paradox + Superpowers 패턴 통합
   
3. **"Invisible Failure Detection" 연구**:
   - 초보자가 자신의 실패를 인지하지 못하는 문제 → detection 메커니즘 설계
   - "A paradox of AI fluency"의 invisible failure 개념을 진단 도구로 발전

4. **Research Agent 구현 착수**: 
   - writing-plans로 구현 계획 수립 → subagent-driven-development로 실행
   - Superpowers 패턴(특히 systematic-debugging, test-driven-development)을 Research Agent에 통합

5. **NotebookLM 파이프라인 안정화**:
   - `nlwflow note-wiki` 인증 문제 해결 방법 탐색 지속
   - 대안: CLI-less notebooklm 접근법 또는 다른 multi-source 분석 도구

## 연구 동향

- **AI Fluency Gap의 과학적 입증**: Potts & Sudhof (2026-04-28)의 "Paradox of AI Fluency"는 초보자-전문가 간극이 단순한 기술 차이가 아니라 **상호작용 모드의 근본적 차이**(수동적 vs. 적극적)임을 실증. 이는 우리 연구의 핵심 가설을 뒷받침.
- **Vibe Coding 연구 급증**: 2026년 4월 arXiv에만 8편 이상의 vibe coding 관련 논문. 초보자 접근성과 교육적 응용이 주류 화두로 정착.
- **Multi-Agent 시스템의 재귀적 확장**: RecursiveMAS는 단순한 "에이전트 여러 개 쓰기"를 넘어, 협업 자체를 scaling axis로 보는 새로운 관점 제시.

## 스킬 업그레이드 기록

- refinement-methodology: v1.3.2 → v1.4.0
- meta-evolution-pattern: v1.3.2 → v1.4.0

## 참고 자료

### 신규 논문 (2026-04-29)
- **2604.25905v1**: "A Paradox of AI Fluency" — Potts & Sudhof (Stanford/UIUC), WildChat-4.8M 기반
  - 링크: https://arxiv.org/abs/2604.25905
  - 코드/데이터: https://github.com/bigspinai/bigspin-fluency-outcomes
- **2604.25917v1**: "Recursive Multi-Agent Systems" — Yang, Zou et al. (UIUC/Stanford/MIT)
  - 링크: https://arxiv.org/abs/2604.25917
  - 코드: https://recursivemas.github.io

### 기존 Wiki 페이지
- `concepts/vibe-coding-beginner-methodology.md` — 초보자 바이브 코딩 방법론
- `concepts/vibe-coding-blueprint-pattern.md` — Plan-First 설계 패턴
- `entities/research-agent-설계-계획.md` — Research Agent 설계
- `entities/superpowers.md` — Superpowers 플러그인 분석
- `entities/subagent-driven-development.md` — Two-Stage Review 패턴
- `queries/vibe-coding-beginner-checklist.md` — 실행 체크리스트

### 기존 논문 (raw/papers/)
- `ai-novice-to-senior-research.md` — 12개 논문 리서치 (2026-04-26)
- `2512.02750v1` — "Can You Feel the Vibes?" (Gama et al.)
- `2604.22747v1` — "Code for All: Vibe Coding Hackathon" (Chen et al.)
- `2510.00328v1` — "Vibe Coding in Practice" (Fawzy et al.)
- `2508.00952v1` — "Academic Vibe Coding" (Crowson & Celi)
- `2604.22604v1` — "Vibe Coding for Clinicians" (Ong et al.)
- `2507.22085v2` — "BOOP: Write Right Code" (Goenka & Thakkar)
- `2604.24758v1` — "Personalized Worked Example Generation" (Pitts et al.)
- `2604.24737v1` — "Learning to Think from Multiple Thinkers" (Joshi et al.)

### 인프라
- `docs/human-in-the-loop-checkpoints.md` — HitL 6단계 체크포인트
- `docs/hyperagents-meta-agent-analysis.md` — HyperAgents 분석