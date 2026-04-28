---
title: "바이브 코딩 초보자 체크리스트"
tags: [ai, vibe-coding, beginner, checklist, workflow]
created: 2026-04-28
updated: 2026-04-28
type: query
status: draft
source_urls:
  - https://codingwithvibe.com/the-complete-guide-to-vibe-coding/
  - https://www.softr.io/blog/vibe-coding-best-practices
  - https://interviewkickstart.com/blogs/articles/vibe-coding-best-practices
  - https://aitoolsclub.com/the-ultimate-vibe-coding-guide-for-beginners-pros/
  - https://github.com/analyticalrohit/awesome-vibe-coding-guide
  - https://www.datacamp.com/blog/vibe-coding-guide-for-beginners
---

# 바이브 코딩 초보자 체크리스트

## 📋 기획 단계
- [ ] 제품 비전 2~3문장으로 정리 (무엇을·누구를 위해·왜)
- [ ] PRD(제품 요구사항 문서) 작성 (AI 초안 → 수정)
- [ ] 와이어프레임 스케치 (Figma/Miro 등)
- [ ] 데이터 구조·사용자 역할 먼저 정의
- [ ] 기술 스택 선택 (AI가 잘 아는 대중적 스택 권장)

## ⚙️ 환경 설정
- [ ] Git/GitHub 초기화 (생명줄!)
- [ ] `.cursorrules` (또는 유사 규칙 파일) 설정
- [ ] `.env` 파일로 API 키·비밀번호 관리

## ✍️ 프롬프트 & 구현
- [ ] 3계층 프롬프트 구조 적용 (How → What → Care)
- [ ] 기능을 작은 단계로 분할
- [ ] "계획 먼저, 코드는 나중에" 패턴 사용
- [ ] 구체적 제약 조건 명시 (버전·라이브러리 제한)
- [ ] 마일스톤마다 Git 커밋

## ✅ 검증 & 보안
- [ ] 즉시 로컬 실행·테스트
- [ ] "무엇이 잘못될 수 있을까?" → AI에 자체 비판 요청
- [ ] 보안 감사: 하드코딩 키 확인, 입력 검증 체크
- [ ] 에지 케이스(빈 입력·네트워크 단절) 확인

## 🐛 디버깅 & 마무리
- [ ] 에러 시 콘솔 전체 AI에 전달
- [ ] 2~3회 실패 시 되돌리기(Revert) → 프롬프트 재작성
- [ ] 생성 → 검토 → 리팩토링 최소 2사이클

## 🔗 관련
- [[vibe-coding-beginner-methodology]] — 상세 방법론
- [[research-agent-설계-계획]] — 초보자 AI 협업 연구