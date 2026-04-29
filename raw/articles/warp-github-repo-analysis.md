---
source_url: https://github.com/warpdotdev/warp
ingested: 2026-04-29
sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
---

# Warp GitHub Repository Analysis

## Source
- **Repository:** https://github.com/warpdotdev/warp
- **Language:** Rust
- **Files:** 3,149 source files
- **License:** AGPL v3 (core) + MIT (WarpUI framework)

## Graphify Analysis Results

- **Nodes:** 52,578
- **Edges:** 246,301
- **Communities:** 975
- **Extraction quality:** 34% EXTRACTED, 66% INFERRED (avg confidence 0.8)
- **Token cost:** 0 (AST-only, no LLM calls)

## Key Findings

1. **WarpUI is a custom Rust UI framework** built from scratch with Entity-Component-Handle pattern
2. **AI Agent system** is deeply integrated: 22 submodules covering agent, MCP, skills, facts, ambient agents, execution profiles
3. **Agent SDK** supports external CLI agents (Claude Code, Codex, Gemini CLI)
4. **Warp Drive** provides cloud sync for workflows, notebooks, MCP configs, env vars
5. **Diesel ORM + SQLite** for local persistence, GraphQL/WebSocket for server sync
6. **Feature Flag system** prefers runtime checks over compile-time cfg directives
7. **63 crates** in Cargo workspace with Tokio, NuShell, Alacritty, Hyper as key deps