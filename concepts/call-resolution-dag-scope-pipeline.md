---
title: Call-Resolution DAG + Scope-Resolution Pipeline
created: 2026-05-02
updated: 2026-05-02
type: concept
tags: [code-intelligence, call-resolution, scope-resolution, language-provider, mro, static-analysis]
confidence: high
source: GitNexus ARCHITECTURE.md § Call-Resolution DAG + Scope-Resolution Pipeline
---

# Call-Resolution DAG + Scope-Resolution Pipeline

**코드 지식 그래프에서 함수/메서드 호출을 해석하는 두 가지 패러다임 — Legacy DAG와 Registry-Primary Pipeline.**

GitNexus는 언어별로 하나의 `LanguageProvider`를 구현하면 두 경로 모두에서 동작하며, migrated 언어는 두 경로의 CI parity를 통과해야 한다.

## 레거시: Call-Resolution DAG (6-Stage)

`parse` 단계 내에서 실행되는 타입드 6단계 파이프라인. 언어 동작은 두 지점의 `LanguageProvider` 후크에서만 주입.

```
extract-call → classify-form → infer-receiver → select-dispatch → resolve-target → emit-edge
     (1)            (2)            (3) [hook]       (4) [hook]         (5)            (6)
```

### 각 단계

| 단계 | 산출물 | 위치 |
|------|--------|------|
| 1. extract-call | `ExtractedCallSite` (name, form, receiver, argCount) | `call-extractors/` (per-language, worker 실행) |
| 2. classify-form | `callForm` (`free`/`member`/`constructor`) + arity | `call-analysis.ts` → `inferCallForm` (공유) |
| 3. infer-receiver | `ReceiverEnriched` (receiver type 확정) | 공유 기본 → `inferImplicitReceiver` 후크 |
| 4. select-dispatch | `DispatchDecision` (primary, fallback, ancestryView) | `selectDispatch` 후크 → 공유 기본 fallback |
| 5. resolve-target | `TieredCandidates` | `lookupMethodByOwnerWithMRO` (MRO walk) |
| 6. emit-edge | CALLS 엣지 | `call-processor.ts`, confidence tier 포함 |

### Provider Hooks

**`inferImplicitReceiver`** — Ruby의 implicit-self 호출을 재작성.
- 입력: `calledName`, `callForm`, `receiverName`, `receiverTypeName`, `callNode`(AST), `filePath`
- 반환: `ImplicitReceiverOverride | null` (null = 기존 유지)

**`selectDispatch`** — 디스패치 전략 선택.
- `primary`: `owner-scoped` (MRO walk) / `free` / `constructor`
- `fallback`: `free-arity-narrowed` (Ruby: implicit-self miss 후 arity-only fallback)
- `ancestryView`: `instance` / `singleton` (Ruby `def self.foo`)

### MRO 전략

언어별 상속 체인 탐색:
- `first-wins` — Java, C#, C++, TS, Ruby, Go
- `c3` — Python (C3 linearization)
- `ruby-mixin` — Ruby (prepend/include/extend 순서)
- `none` — 단일 상속 언어

## 신규: Scope-Resolution Pipeline (RFC #909 Ring 3)

**Registry-primary** 방식. 언어는 `ScopeResolver` 인터페이스 하나만 구현하면 된다. Migrated 언어(Python, C#, TypeScript)에서는 레거시 DAG가 gate off 된다.

### 파이프라인 단계

```
ParsedFile[]  (extractParsedFile per file)
   │  finalizeScopeModel (+ provider hooks)
   ▼
ScopeResolutionIndexes
   │  resolveReferenceSites  (MethodRegistry.lookup)
   ▼
ReferenceIndex
   │  emitReceiverBoundCalls  ── FIRST
   │  emitFreeCallFallback    ── THEN
   │  emitReferencesViaLookup ── LAST
   │  emitImportEdges
   ▼
KnowledgeGraph  (IMPORTS / CALLS / ACCESSES / INHERITS / USES)
```

### ScopeResolver 계약

| Hook | 목적 |
|------|------|
| `languageProvider` | 기본 `LanguageProvider` (tree-sitter 쿼리, 캡처, 임포트/바인딩 인터프리터) |
| `populateOwners(parsed)` | 지연된 `ownerId` 채우기 (Python 클래스 본문) |
| `buildMro(graph, parsed, nodeLookup)` | MRO 생성 (C3, Ruby-mixin, first-wins) |
| `resolveImportTarget(target, fromFile, allFiles)` | PEP-328 등 import 경로 해석 |
| `mergeBindings(existing, incoming, scopeId)` | Shadowing / LEGB 우선순위 |
| `arityCompatibility` | MethodRegistry.lookup Step 2 |
| `importEdgeReason` | IMPORTS 엣지 confidence tier |
| `propagatesReturnTypesAcrossImports?` | Cross-file return type 전파 (default on) |
| `fieldFallbackOnMethodLookup?` | Static 언어는 OFF (over-connection 방지) |
| `unwrapCollectionAccessor?` | `data.Values` 패턴 (default off) |
| `collapseMemberCallsByCallerTarget?` | Edge 중복 제거 (default off) |
| `populateNamespaceSiblings?` | Cross-file implicit namespace visibility (C#) |
| `hoistTypeBindingsToModule?` | Module-level type binding lookup |

### 언어 등록

1. `languages/<lang>/scope-resolver.ts`에 `ScopeResolver` 구현
2. `SCOPE_RESOLVERS`에 등록
3. Shadow-harness corpus parity ≥ 99% fixtures / ≥ 98% corpus → `MIGRATED_LANGUAGES` 추가

### Same-Graph Guarantee

두 경로가 생성하는 엣지는 다운스트림에서 **구별 불가능**:
- **Node ID** — 동일한 `generateId()`, 동일한 qualified-name keyspace
- **Edge vocabulary** — 동일한 reason set (`import-resolved`, `global`, `local-call`, `same-file`, `interface-dispatch`, `read`, `write`)
- **Confidence tier** — 동일한 numeric scale

CI parity 워크플로우(`ci-scope-parity.yml`)가 모든 PR에서 두 경로의 출력을 비교하고 divergences를 fail 시킨다.

### SemanticModel: 단일 진실 공급원

`ParsedFile` = AST-level truth (두 경로가 공유)
`SemanticModel` = Symbol-level truth (symbol-indexed lookups)

**Write/Read Phase Contract:**
```
Phase 1: legacy parse     → symbolTable.add → types/methods/fields
Phase 2: scope-resolution → reconcileOwnership() → corrected ownerIds
Phase 3: finalize         → model.attachScopeIndexes(bundle) — freeze
─────────────────────── phase boundary ────────────────────────
Read phase: all resolution passes + MCP + HTTP + embeddings
            see SemanticModel (read-only); writes = type-error
```

## 성능 최적화

- **Cross-phase Tree cache** — parse 단계가 `scopeTreeCache`에 Tree 저장, scope-resolution이 재파싱 없이 읽음
- **Workspace-resolution-index** — `findOwnedMember` / `findExportedDef` O(1) lookup
- **SCC-ordered cross-file propagation** — multi-hop alias chain을 linear pass로 해결 (PR #1050)

## 관련 항목

- [[GitNexus]] — 구현체
- [[knowledge-graph-code-intelligence]] — 전체 패러다임
- Tree-sitter Unified Capture ⚠️ 페이지 미생성 — 통합 캡처 태그
- Scope Resolution Contract ⚠️ 페이지 미생성 — ScopeResolver 계약 상세
- Language Provider Pattern ⚠️ 페이지 미생성 — LanguageProvider 추상화 패턴