---
title: Scrapling
created: 2026-05-02
updated: 2026-05-02
type: entity
tags: [scraping, python, stealth, spider]
language: Python
version: 0.4.7
repo: https://github.com/D4Vinci/Scrapling
authors: Karim Shoair
---

# Scrapling

탐지 불가능한 웹 스크래핑 라이브러리. `curl_cffi`, Playwright/Patchright, anti-detection을 3-tier Fetcher 계층으로 통합한 파이썬 패키지.

## 핵심 아키텍처

### 3-Tier Fetcher Hierarchy (가장 독특한 설계)

```
Fetcher/AsyncFetcher         ← Tier 1: curl_cffi 기반 정적 HTTP
    ↓
DynamicFetcher               ← Tier 2: Playwright/Patchright 크로미움
    ↓
StealthyFetcher              ← Tier 3: Anti-detection 완전 스텔스
```

- **Tier 1 — `Fetcher`/`AsyncFetcher`** (`scrapling/fetchers/requests.py`, 28줄): 단 4개 HTTP 메서드(GET/POST/PUT/DELETE)만 `BaseFetcher`에 위임. `__FetcherClientInstance__` singleton engine에 연결.
- **Tier 2 — `DynamicFetcher`** (`scrapling/fetchers/chrome.py`, 97줄): Playwright/Patchright 브라우저로 JS 렌더링, headless/headful 모드, 리소스 차단, `page_action` 자동화 페이로드 지원.
- **Tier 3 — `StealthyFetcher`** (`scrapling/fetchers/stealth_chrome.py`, 115줄): `browserforge` + `apify-fingerprint-datapoints` 지문 위장, Patchright 엔진으로 Cloudflare 우회.

### Lazy Import Architecture

```python
# scrapling/__init__.py
_LAZY_IMPORTS = {
    "Fetcher": ("scrapling.fetchers", "Fetcher"),
    "Selector": ("scrapling.parser", "Selector"),
    ...
}

def __getattr__(name):
    # 실제 접근 시점에만 import
    module = __import__(module_path, fromlist=[class_name])
    return getattr(module, class_name)
```

TYPE_CHECKING으로 타입만 선점, 런타임 로딩은 지연시켜 cold-start 속도 최적화. `scrapling/fetchers/__init__.py`도 동일 패턴.

### Selector (파서)

`scrapling/parser.py` — 1,377줄의 단일 파일. `lxml` 기반 CSS/XPath 듀얼 엔진:

- `Selector(SelectorsGeneration)` — `css_to_xpath` 변환, pre-compiled XPath (`_find_all_elements`, `_find_all_text_nodes`)
- `TextHandler`/`TextHandlers` — 메서드 체이닝 (`.re()`, `.json()`, `.clean()`)
- `AttributesHandler` — `class_` → `class` Python 키워드 매핑
- `SQLiteStorageSystem` — 적응형 엔진용 요소 저장 DB

### Spider Framework (크롤링 엔진)

`scrapling/spiders/` — 완전한 크롤링 프레임워크:

| 모듈 | 역할 |
|------|------|
| `Spider` (`spider.py`, 323줄) | ABC 기본 클래스. `start_urls`, `concurrent_requests`, `download_delay`, pause/resume (`crawldir`) |
| `CrawlerEngine` (`engine.py`) | 비동기 크롤링 루프, 세션 병렬 처리 |
| `Request` (`request.py`) | 콜백 체인, 직렬화/복원 |
| `SessionManager` | 세션 라이프사이클 관리, 타임아웃/재시도 |
| `Scheduler` | 도메인별 속도 제한, 우선순위 큐 |
| `CheckpointData` | 크롤링 상태 주기적 저장 (5분 간격 기본) |
| `ResponseCacheManager` | 개발 중 HTTP 응답 디스크 캐싱 |
| `RobotsTxtManager` | 도메인별 robots.txt 파싱, crawl-delay 준수 |
| `CrawlResult`/`CrawlStats` | 결과 아이템 컬렉션, JSON/JSON Lines 내보내기 |

## Graphify 분석

- **2,498 nodes · 7,738 edges · 66 communities**
- Extraction: 36% EXTRACTED · 64% INFERRED (AST 기반)
- God Nodes: `Request`(455), `SessionManager`(267), `Response`(253), `CrawlStats`(199), `TextHandler`(170)
- Community 0: Async/Dynamic 세션 믹스인
- Community 1: 체크포인트/상태 저장
- Community 2: 크롤링 결과/통계
- Community 9: 동적 세션 관리

## 추가 기능

- **PlaywrightShell** (`scrapling/core/shell.py`): Python 객체로 브라우저 제어 (실제 Playwright 세션에 연결)
- **AI 확장** (`scrapling/core/ai.py`): LLM 기반 요소 선택
- **CLI** (`scrapling/cli.py`): 커맨드라인 인터페이스
- **proxy_rotation** (`engines/toolbelt/proxy_rotation.py`): 프록시 순환
- **fingerprints** (`engines/toolbelt/fingerprints.py`): 지문 데이터 관리
- **block_ads**: `ad_domains.py`에 3,500+ 알려진 광고/트래킹 도메인 목록

## 의존성

- `lxml` + `cssselect` — 파싱
- `orjson` — 고속 JSON
- `curl_cffi` — Tier 1 fetcher
- `playwright` + `patchright` — Tier 2/3
- `browserforge` + `apify-fingerprint-datapoints` — 안티 디텍션
- `anyio` — 비동기 통합

## 라이선스

BSD License