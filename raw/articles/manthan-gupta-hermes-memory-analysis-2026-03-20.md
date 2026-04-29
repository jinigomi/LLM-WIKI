---
source_url: https://x.com/manthanguptaa/status/2034849672985288957
ingested: 2026-04-30
author: Manthan Gupta
title: "I Read Hermes Agent's Memory System, and It Fixes What OpenClaw Got Wrong"
published: 2026-03-20
sha256: b8e2a15c7d3f4e5a6b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a
---

# I Read Hermes Agent's Memory System, and It Fixes What OpenClaw Got Wrong

If you've read my previous posts on ChatGPT memory, Claude memory, and OpenClaw memory, you already know I keep coming back to the same question: how do these agents actually remember?

Hermes Agent was particularly interesting to me because this time, I did not have to reverse engineer everything from behavior alone. Hermes is open source, and both the repo and the docs are public. So instead of poking a black box with prompts, I went straight to the code paths that build prompt state, persist sessions, flush memories, and query past conversations.

The short version is this: Hermes does not have one memory system. It has four.

1. A very small, curated prompt memory stored in `MEMORY.md` and `USER.md`.
2. A searchable SQLite archive of past sessions exposed through `session_search`.
3. Agent-managed skills that act like procedural memory
4. An optional Honcho layer for deeper user modeling

And the key design choice tying all of this together is simple: keep the prompt stable for caching, and push everything else to tools.

## Hermes's Context Structure

Before understanding memory, it helps to understand what Hermes is actually sending to the model.

The system prompt is assembled roughly like this: persona → memory → skills index → conversation → tool schemas. This matters because Hermes is optimizing for provider-side prompt caching. The prompt builder is very explicit about this in the source: the stable prefix should stay stable for as long as possible.

That one decision explains most of Hermes's memory architecture. If a piece of information should be available on every turn, Hermes tries to keep it tiny and inject it once. If it is large, historical, or only occasionally useful, Hermes pushes it out of the prompt and retrieves it on demand.

## Layer 1: Frozen Prompt Memory

The built-in memory system is surprisingly small. Hermes stores durable memory in two files under `~/.hermes/memories/`: `MEMORY.md` (~2,200 chars target) and `USER.md` (~1,375 chars target). That is roughly 1,300 tokens combined. And that is deliberate.

At session start, Hermes loads both files, renders them into a prompt block, and then freezes that snapshot for the rest of the session. Mid-session writes are persisted to disk immediately, but they do not mutate the already-built system prompt. Those changes only show up when a new session starts, or after a compression-triggered prompt rebuild.

Key design choices:
1. **Character limits, not token limits** — keeps memory logic model-agnostic
2. **Simple delimiter-based format** — entries separated with `§`, no vector DB, just plain text
3. **Intentionally tiny** — only the highest-value facts in prompt memory
4. **Curated state, not a diary** — explicit instructions to save preferences, environment facts, corrections, conventions (NOT task progress, session outcomes, temporary TODO state)

The `memory` tool has three actions: `add`, `replace`, `remove`. It uses substring matching — no internal IDs needed. It also rejects exact duplicates and blocks dangerous content (prompt-injection patterns, credential exfiltration, SSH backdoor hints, invisible Unicode).

## Layer 2: session_search for Episodic Recall

If MEMORY.md and USER.md are Hermes's hot memory, then session_search is its long-tail recall system. All past sessions are stored in `~/.hermes/state.db`, a SQLite database with sessions table, messages table, FTS5 full-text search index, and lineage links through parent_session_id.

Pipeline: search → summarize → inject. Hermes is basically saying: keep the always-injected memory tiny, store the real history in SQLite, search that history only when needed, summarize the results before handing them back.

The docs describe session_search as a way to answer: "Did we discuss this last week?", "What did we do about X?", "As I mentioned before..."

## Layer 3: Compression and Memory Flush

As sessions grow, Hermes eventually summarizes the middle portion to stay within context window. But summarization is lossy. So Hermes does a memory flush first — before compression, it injects a synthetic instruction that basically says "save anything important to memory now", then runs one extra model call with only the memory tool available.

The flow: session grows → memory flush (model saves durable bits) → compression (middle summarized) → prompt rebuilt (fresh memory snapshot).

## Layer 4: Skills as Procedural Memory

Skills live under `~/.hermes/skills/` and act like reusable knowledge documents — the agent's procedural memory. When Hermes discovers a non-trivial workflow, fixes a tricky issue, or learns a better way to do something, it can save that as a skill and reuse it later.

Token-efficiency: Hermes injects a compact skills index and only loads full skill content when needed — keeping procedural memory available without paying the full token cost every turn.

## Layer 5: Honcho for Deeper User Modeling

Optional Honcho layer adds: cross-session user modeling, cross-machine/cross-platform continuity, semantic search over user context, dialectic LLM-generated answers about the user or the AI peer.

The integration is cache-aware: on first turn, prefetched Honcho context is baked into the cached system prompt. On later turns, Hermes avoids mutating the stable system prompt — attaches Honcho recall to the current user turn at API-call time only. Honcho also models both the user AND the AI assistant.

## How Hermes Differs from OpenClaw

| Hermes | OpenClaw |
|--------|----------|
| Prompt memory aggressively bounded | Memory closer to Markdown-first storage |
| Session history in SQLite | Daily logs + long-term memory files as primary source |
| Past work recalled through session_search | Memory recall leans on hybrid search over stored notes |
| Procedural memory in skills | — |
| Deeper user modeling optional (Honcho) | — |

The key insight: Hermes is more cache-aware. OpenClaw leans harder into "memory as searchable stored knowledge." Hermes leans harder into "memory as a hot working set plus cold retrieval layers."

## What Hermes Gets Right

1. **Separates hot memory from cold recall** — small prompt memory for what always matters, search for what only sometimes matters
2. **Treats prompt stability as a first-class constraint** — frozen snapshots, delayed prompt updates, turn-level Honcho injection
3. **Acknowledges that memory is plural** — semantic profile memory, episodic session recall, procedural memory (skills), optional higher-order user modeling (Honcho)

## Conclusion

Hermes's memory system is a layered continuity architecture: tiny curated prompt memory at the center, searchable SQLite history around it, skills system for procedural reuse beyond that, and optional Honcho user model on top. The design principle: memory should help the agent stay useful without destroying prompt stability. Not remembering more — remembering the right things, in the right layer, at the right cost.

## References
- Hermes Agent GitHub Repository
- Hermes Persistent Memory Docs
- Hermes Prompt Assembly Docs
- Hermes Session Storage Docs
- Hermes Skills Docs
- Hermes Honcho Docs