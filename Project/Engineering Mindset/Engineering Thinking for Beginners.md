---
type: skill-index
created: 2026-04-25
tags: [engineering-thinking, beginner, skill]
description: Hermes AI 에서 사용하는 스킬의 개요 및 레퍼런스
---

# Engineering Thinking for Beginners (Hermes Skill)

> Hermes AI에서 사용하는 스킬입니다. 실제 스킬 파일은 `skills/engineering-thinking-beginner/`에 있습니다.

---

## 스킬 개요

**대상:** 엔지니어링적 사고가 없는 초보자

**핵심 철학:**
> *"AI가 코드를 만드는 시대, 가장 중요한 건 **뭘 만들지 결정하는 능력**과 **문제를 기억하는 습관**이다. 코딩 실력은 나중에 따라온다."*

---

## 활성화 조건

이 스킬이 활성화되는 조건:
- 사용자가 "프로젝트", "만들고 싶다", " 앱", "웹사이트" 언급
- 기존 프로젝트의 맥락이 사라졌을 때 (3일 이상 경과)
- 에러나 문제로 막혔을 때

**시작 프롬프트:**
```
"새 프로젝트를 시작하고 싶으시군요. 먼저 몇 가지 질문에 답해주시면 
정리된 프로젝트를 만들어드리겠습니다. 부담 갖지 마세요 -
정답은 없고, 대답할 수 없는 건 '모르겠다'고 하시면 됩니다."
```

---

## 5단계 워크플로우

| Phase | 이름 | 핵심 질문 |
|-------|------|----------|
| 1 | Framing | "어떤 문제를 해결하고 싶나요?" |
| 2 | Discovery | "기술 스택 선호가 있나요?" |
| 3 | Architecture | "이런 구조로 가면 어떨까요?" |
| 4 | Roadmap | "한 단계씩 해볼게요" |
| 5 | Execution | "설계 → 구현 → 기록" |

---

## 핵심 원칙

1. **"모르는 것을 묻지 마라"** — 정답을 제시하고 이유를 제공
2. **"증거 기반 권장"** — 단순 추천이 아니라 근거를 제시
3. **"체크리스트로 불안 해소"** — 완료된 항목 = 성취감
4. **"70% 완료 규칙"** — 70%만 확정되면 구현 시작
5. **"기록은 선택이 아니다"** — 기록 안 하고 후회한 적 많았음

---

## 에러 에스컬레이션

| 레벨 | 책임자 | 판단 기준 |
|------|--------|----------|
| 1 | Claude Code | 한 파일, 한 줄 단위 |
| 2 | 시니어 (Claude Desktop) | 반복되는 에러, 구조적 문제 |
| 3 | 설계 수정 | 코드 수준에서 해결 불가 |

---

## 파일 위치

| 위치 | 설명 |
|------|------|
| `skills/engineering-thinking-beginner/SKILL.md` | 메인 스킬 파일 |
| `skills/engineering-thinking-beginner/references/` | 레퍼런스 파일들 |

---

## Obsidian 프로젝트 문서

실제 프로젝트 진행 시 사용할 문서들: [[Home]]

---

## Related

- [[Home]] — Obsidian 프로젝트 문서들 (Ch.9 구조)
- [[Engineering Thinking for Beginners/SKILL.md]] — Hermes 스킬 소스