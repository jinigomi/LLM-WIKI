---
title: "Memcord"
type: entity
created: "2026-05-02"
tags: [mcp, memory, summarization, self-hosted, privacy]
---

## Overview

**Memcord** v3.4.0 — privacy-first, self-hosted MCP server for AI agent memory management.
Author: [@ukkit](https://github.com/ukkit), MIT License.
Python 3.10+, 80 source files, ~223K words.

Graphify: **4,166 nodes · 14,275 edges · 54 communities** (30% EXTRACTED, 70% INFERRED, avg confidence 0.56)

## Architecture

### Core God Nodes (betweenness centrality)

| Node | Edges | Betweenness | Role |
|------|-------|-------------|------|
| `ChatMemoryServer` | 552 | 0.323 | Cross-community bridge: connects Communities 0,2,3,5,10,11,16,17,18 |
| `StorageManager` | 522 | 0.224 | 10-community bridge: 0,1,2,3,4,6,10,11,18,19,20 |
| `SearchQuery` | 457 | 0.125 | 5-community bridge: 0,1,2,3,5,8 |
| `MemoryEntry` | 447 | — | Core data model (Pydantic, manual_save/auto_summary) |
| `MemorySlot` | 417 | — | Slot storage unit |
| `SlotConfig` | 205 | — | Per-slot configuration |
| `TextSummarizer` | 201 | — | Extractive summarization engine |
| `ContentCompressor` | 195 | — | Compression pipeline |

### Key Communities (≥0.05 cohesion)

- **Community 0** (738 nodes, cohesion 0.0): `ChatMemoryServer` + optimized schemas + tool dispatching
- **Community 1** (326, 0.02): `ArchivalManager` + `ArchiveEntry` + archive index
- **Community 3** (224, 0.04): `ABC` → `BaseSummarizer` + `MemcordError` + `ErrorHandler` — error/exception hierarchy
- **Community 8** (79, 0.03): `CacheManager` — `DiskCache`, `LRUCache`, `CacheStats`, eviction policies
- **Community 9** (102, 0.08): `MonitoringService` — `DiagnosticsReport`, `HealthCheck`, `MetricsReport`
- **Community 10** (109, 0.07): `ArchiveService` — long-term slot archiving
- **Community 11** (97, 0.06): `CompressionService` — `BulkCompressionResult`, `CompressionAnalysis`
- **Community 12** (36, 0.03): `TextSummarizer` — MMR extractive + sentence splitting
- **Community 13** (51, 0.04): `ImportService` + `PDFHandler` — PDF/web import pipeline
- **Community 19** (20, 0.06): `MemoryOptimizer` + `MemoryLeakDetector` + `ObjectPool`
- **Community 20** (22, 0.09): `SelectEntryService` — timeline navigation ("2 hours ago")
- **Community 21** (24, 0.15): `handle_errors()` decorator + `validate_slot_selected()`

## Data Architecture

### Memory Slot Model

```
memory_slots/{name}.json
├── MemorySlot
│   ├── MemoryEntry[] (type: manual_save | auto_summary, max 10MB)
│   │   └── CompressionInfo (algorithm, ratio, timestamps)
│   ├── tags[], group_path, description, priority
│   ├── SlotConfig (summarizer_backend, sumy_algorithm, etc.)
│   └── metadata (total_entries, total_characters, last_summary_at)
├── archives/{name}_archived.json + index.json
├── shared_memories/{name}.md, .txt
```

### Storage Layers

1. **`MemoryEntry`** — 1 char to 10MB content, Pydantic-validated, datetime + metadata
2. **`MemorySlot`** — collection of entries + SlotConfig + grouping
3. **`StorageManager`** — JSON file read/write, compression info tracking
4. **`ArchivalManager`** — compressed archive + index

## Summarizer Backend Hierarchy

```
ABC → BaseSummarizer
├── TextSummarizer (nltk)     — extractive, fast, built-in
├── TextSummarizer (sumy)     — graph-based extractive, LexRank/LSA/Edmundson, default
├── SemanticSummarizer        — sentence-transformers embedding-based, ~80MB
└── TransformersSummarizer    — BART abstractive, ~400MB, best quality
```

`SummarizerFactory` selects backend per `SlotConfig.summarizer_backend`.
`MEMCORD_SUMMARIZER` env var can override globally.

## Tool Set (28 tools, MCP spec 2025-03-26 / 2025-11-25)

Two modes:

| Mode | Count | Tools |
|------|-------|-------|
| **Basic** (default) | 15 | `memcord_name`, `save`, `read`, `save_progress`, `list`, `search`, `query`, `configure`, `zero`, `select_entry`, `merge`, `init`, `unbind`, `ping`, `close` |
| **Advanced** (`MEMCORD_ENABLE_ADVANCED=true`) | 23+ | Basic + `tag`, `list_tags`, `group`, `import`, `compress`, `export`, `share`, `archive`, `clear`, `reset` |

v3.4.0 MCP compliance: `ToolAnnotations` (readOnly/destructive/idempotent hints on all tools), Anthropic result-size extension, `ResourceTemplate` with slot-name autocompletion, progress notifications with `message` field.

## Key Design Patterns

- **Slot Resolution Priority**: explicit `slot_name` → active slot (`memcord_use`) → `.memcord` binding file → error
- **Auto-Activation**: reading via `.memcord` binding auto-activates the slot for the session
- **Privacy Mode (`memcord_zero`)**: prevents all saves — nothing persisted
- **Idempotent Hooks (`--install-hooks`)**: auto-save before context compaction + session end, merges without overwrite
- **Lazy Backend Loading**: transformer/semantic backends only import when configured → cold-start stays fast
- **Path Security**: SSRF protection via `is_private_ip()` + `safe_path()` + `sanitize_filename()`

## Client Integrations

| IDE/Client | Config Template |
|------------|----------------|
| Claude Code CLI | `config-templates/claude-code/` (MCP + hooks) |
| Claude Desktop | `config-templates/claude-desktop/` |
| VSCode + Copilot | `config-templates/vscode/` |
| Google Antigravity | `config-templates/antigravity/` |

`scripts/generate-config.py` auto-generates platform-specific MCP config.

## Surprising Graph Connections

- `ChatMemoryServer` has 513 **INFERRED** edges (70%+ unreviewed) — Graphify flagged 4 nodes with >400 inferred edges each for human verification
- 574 **isolated nodes** (test functions) — suggests test isolation not captured by AST edges
- 31 single-node communities (23–53) — CLI functions/install scripts fragmented by tree-sitter limits

## See Also

- MCP Server — MCP protocol category
- [[Scrapling]] — another Python MCP tool analyzed via Graphify
- [GitHub: ukkit/memcord](https://github.com/ukkit/memcord)