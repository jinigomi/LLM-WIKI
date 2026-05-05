---
title: Anime.js
created: 2026-05-02
updated: 2026-05-02
type: entity
tags: [animation, javascript, library, open-source, web, svg, css, waapi, engine]
sources: [https://github.com/juliangarnier/anime]
confidence: high
---

## Overview

Anime.js는 **JavaScript 애니메이션 엔진**이다. Julian Garnier가 만든 오픈소스(MIT) 프로젝트로, v4.4.1 기준 ES 모듈 아키텍처로 제공된다. CSS 프로퍼티, SVG, DOM 속성, JavaScript 객체를 단일 API로 애니메이션할 수 있다. V4는 V3에서 완전히 재작성된 버전으로, ES modules, TypeScript 타입, WAAPI 통합, Draggable, Scope 등이 새롭게 추가되었다.

**핵심 철학:** "fast, multipurpose and lightweight" — 애니메이션 라이브러리의 복잡성을 숨기면서도 모든 렌더링 타겟(CSS/SVG/JS/WAAPI/Canvas/WebGL)을 하나의 API로 추상화한다.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      Public API                          │
│  animate()  createTimeline()  createAnimatable()         │
│  createDraggable()  createScope()  stagger()  utils      │
├─────────────────────────────────────────────────────────┤
│                    Animation Layer                        │
│  JSAnimation  WAAPIAnimation  Additive  Composition      │
├─────────────────────────────────────────────────────────┤
│                    Timeline Layer                         │
│  Timeline (extends Clock)  add()  set()  call()          │
├─────────────────────────────────────────────────────────┤
│                    Core Layer                             │
│  Clock  Engine  Render  Values  Transforms  Colors       │
│  Units  Styles  Targets  Helpers  Consts  Globals        │
├─────────────────────────────────────────────────────────┤
│                    Specialized Modules                     │
│  Draggable  Animatable  Scope  ScrollObserver             │
│  SVG (Drawable/MorphTo/MotionPath)  Text (Split/Scramble) │
│  Easings (Spring/Steps/CubicBezier/Eases/Linear/Irregular)│
│  Layout  Timer  Stagger  Random                           │
├─────────────────────────────────────────────────────────┤
│                    Rendering Targets                       │
│  CSS properties  SVG attributes  DOM attributes           │
│  JavaScript Objects  WAAPI  Canvas  WebGL                 │
└─────────────────────────────────────────────────────────┘
```

### God Nodes (Graphify 기준)
| 함수/클래스 | Edges | 역할 |
|------------|-------|------|
| `isUnd()` | 39 | 타입 체크 유틸리티 — 모든 모듈이 의존 |
| `round()` | 36 | 수치 정밀도 — 렌더링 파이프라인 핵심 |
| `get()` | 30 | 값 읽기 — CSS/JS/속성 통합 접근자 |
| `clamp()` | 27 | 값 범위 제한 — 물리/스크롤/easing에 사용 |
| `Draggable` | 26 | 드래그 인터랙션 엔진 |
| `Timer` | 25 | 타이머 — Animation/Timeline의 부모 클래스 |
| `setValue()` | 23 | 값 쓰기 — 모든 애니메이션 출력 담당 |

### 커뮤니티 분석 (Graphify)
- **Community 0 (89 nodes, Cohesion 0.03):** 코어 헬퍼 함수 클러스터 — `addAdditiveAnimation()`, `animate()`, `binarySubdivide()`, `calcBezier()`, `clamp()` 등. 결합도는 낮지만 전체에서 가장 큰 클러스터.
- **Community 1 (72 nodes, Cohesion 0.05):** 색상 변환 + 키프레임 생성 — `generateKeyframes()`, `hexToRgba()`, `hslToRgba()`, `rgbToRgba()`
- **Community 2 (23 nodes, Cohesion 0.05):** Draggable 모듈 — `Draggable`, `preventDefault()`, `Transforms`, `followPointer()`
- **Community 3 (16 nodes, Cohesion 0.07):** 애니메이션 코어 — `Animation`, `Clock`, `Engine`, `interpolate()`
- **Community 4 (27 nodes, Cohesion 0.07):** 파티클 예제 — `animateParticle()`, `createParticule()`, `getRandomDeg()`
- **Community 7 (8 nodes, Cohesion 0.10):** DOM Proxy + Scroll Observer — `DOMProxy`, `ScrollContainer`, `ScrollObserver`
- **Community 12 (4 nodes, Cohesion 0.18):** WAAPI 통합 — `addWAAPIAnimation()`, `WAAPIAnimation`
- **Community 19 (2 nodes, Cohesion 0.67):** 베지어 곡선 — `binarySubdivide()`, `calcBezier()` (고결합 유틸)
- **Community 16 (2 nodes, Cohesion 0.67):** 모션 패스 — `createMotionPath()`, `getPathProgess()`

**의미 구조:** 총 165개 파일, ~290K words. **12개 주요 모듈로 분할**되어 있으며, `Clock → Engine → Timer → Animation → Timeline` 순의 상속 계층이 아키텍처의 중추를 이룬다. `core/`에 유틸리티(helpers, values, colors, transforms, render)를 집중시켜 모든 상위 모듈이 공유한다.

## Key Design Decisions

### 1. Clock 기반 상속 계층
`Clock` 클래스가 `Engine`, `Timer`의 베이스이며, `Animation`과 `Timeline`은 `Timer`를 확장한다. 프레임레이트 제어(`fps`), 재생 속도(`speed`), 델타 타임 계산이 단일 체인으로 통합되어 있다.

### 2. JS + WAAPI 듀얼 렌더링
`JSAnimation`(JavaScript 기반)과 `WAAPIAnimation`(Web Animations API)을 동시에 지원한다. `composition.js`에서 Tween 합성(composite/blend/override)을 처리하고, `additive.js`에서 additive 애니메이션을 별도로 처리한다.

### 3. Decomposed Value Pipeline
CSS 프로퍼티를 `decomposeRawValue()` → `decomposeTweenValue()` → `decomposedOriginalValue()` 순으로 분해하여 숫자+단위+색상을 통합 처리한다. `fromTargetObject` / `toTargetObject`를 단일 인스턴스로 재사용해 GC 압박을 최소화한다.

### 4. Scope 기반 리소스 관리
`Scope` 클래스가 애니메이션, 타이머, 드래거블의 생명주기를 관리한다. `scope.refresh()`로 모든 애니메이션을 초기 상태로 되돌릴 수 있고, `scope.revert()`로 DOM 변경을 롤백할 수 있다. React/Angular refs도 지원.

### 5. Text Split 엔진
`split()` 함수가 DOM 텍스트를 `<span>` 단위로 분해하여 문자/단어/라인별 애니메이션을 가능하게 한다. `scramble()`은 텍스트를 랜덤 문자로 변환하며, 둘 다 `keepTime()`으로 엔진 시간과 동기화된다.

### 6. SVG 전용 모듈
- **`createDrawable()`:** SVG의 `stroke-dashoffset`/`stroke-dasharray`를 이용한 라인 드로잉 애니메이션
- **`createMotionPath()`:** SVG path를 따라 요소 이동, `getPointAtLength()` 기반
- **`morphTo()`:** SVG `<path>`, `<polygon>`, `<polyline>` 간 형태 변환 (정밀도 파라미터 제공)

### 7. 모듈형 번들 전략
ESM(`dist/modules/`), UMD(`dist/bundles/`), CJS를 모두 제공. `sideEffects: false`로 트리쉐이킹 최적화. `exports` 필드로 sub-path imports(`animejs/timer`, `animejs/utils` 등) 제공.

## Easing System
```
easings/
├── cubic-bezier/  — CSS cubic-bezier() 파싱 + 커스텀 커브
├── steps/         — step-start, step-end, steps(n)
├── linear/        — linear, none
├── irregular/     — 불규칙 이징 (사용자 정의 배열)
├── spring/        — 스프링 물리 기반 이징 (mass, stiffness, damping)
└── eases/         — 40+ 프리셋 (inOutSine, outElastic, inBack 등)
```

## Cross-Platform 지원
- **Browser:** `requestAnimationFrame` 기반, `document.hidden` 시 자동 일시정지
- **Node.js:** `setImmediate` 기반 엔진 틱, `isBrowser` 상수로 분기
- **React/Angular:** `Scope`에서 refs 지원

## Strengths / Weaknesses

### 강점
- **통합 API:** CSS/SVG/JS/WAAPI 모두 하나의 `animate()`로 제어
- **경량:** 165개 소스 파일이지만 각 모듈이 독립적, 트리쉐이킹 지원
- **확장성:** `Clock → Timer → Animation` 상속 체인으로 새로운 애니메이션 타입 추가 용이
- **고품질 이징:** 40+ 내장 이징 + Spring 물리 + Custom Cubic Bezier
- **TypeScript 지원:** JSDoc 기반 타입, `.d.ts` 파일 자동 생성
- **성능 최적화:** 객체 풀링(fromTargetObject/toTargetObject), GC 회피, `#__PURE__` 어노테이션

### 약점
- **문서 부족:** V4가 아직 문서화 진행 중 (README만 있고 풀 문서는 WIP)
- **복잡한 내부 구조:** Value decomposition 파이프라인이 깊어서 커스텀 Tween 작성이 어려움
- **Canvas/WebGL 미성숙:** API는 지원하지만 예제/문서가 거의 없음
- **Graphify Cohesion:** 코어 유틸리티(Community 0)의 결합도가 0.03으로 매우 낮음 — 모듈화는 잘 되어있으나 의존성 그래프가 분산되어 있음

## 연결 개념
- [[vibe-coding-beginner-methodology]] — 바이브 코딩에서 애니메이션 라이브러리 선택으로 언급
- [[Qwen-FlashQLA]] — JavaScript 최적화 엔지니어링과 GPU 커널 최적화의 대비
- [[spec-driven-development]] — 애니메이션 라이브러리와 SDD의 관계 (명세 → 구현)