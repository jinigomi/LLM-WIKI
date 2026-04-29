# 연구 결과 저장 SOP

##基本原则

1. 모든 연구 자료는 raw/에 먼저 저장
2. 분석 결과는 entities/ 또는 concepts/에 저장
3. 모든 페이지는 2개 이상 다른 페이지와 wikilinks
4. 품질 기준 미달 시 재연구 또는 보류

## 저장 흐름

```
발견 → raw/articles/ 또는 raw/papers/ → 분석 → entities/ 또는 concepts/ → index.md 갱신 → log.md 기록
```

## 품질 기준

- ArXiv 논문: 3개 이상 출처 확인
- 웹 기사: 신뢰도 높은 출처優先
- 커뮤니티 posts: 검증된 출처 (Reddit/HN/Dev.to 등)

## 파일命名 규칙

- raw/articles/: 주제-출처-날짜.md
- raw/papers/: 주제-저자-날짜.md
- entities/: 주제-형태.md
- concepts/: 주제-개념.md

## 체크리스트

- [ ] 출처 URL 기록
- [ ] 수집 날짜 기록
- [ ] 관련 기존 페이지와 연결
- [ ] index.md 갱신
- [ ] log.md 기록