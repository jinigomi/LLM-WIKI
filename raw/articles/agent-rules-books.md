---
source_url: https://github.com/ciembor/agent-rules-books
ingested: 2026-05-05
---

# ciembor/agent-rules-books

MIT licensed universal project rules for coding agents.
14 classic software engineering books → AI agent rules in 3 tiers (full/mini/nano).

## Release Matrix Summary

| Book | Mini rules | Mini lines | Full rules | Full lines |
|------|-----------|-----------|-----------|-----------|
| A Philosophy of Software Design | 28 | 46 | 177 | 370 |
| Clean Architecture | 31 | 49 | 289 | 515 |
| Clean Code | 29 | 47 | 220 | 297 |
| Code Complete | 38 | 56 | 180 | 354 |
| Designing Data-Intensive Applications | 37 | 55 | 205 | 393 |
| Domain-Driven Design | 37 | 54 | 191 | 380 |
| Domain-Driven Design Distilled | 27 | 43 | 128 | 259 |
| Implementing DDD | 35 | 49 | 171 | 340 |
| Patterns of Enterprise Application Architecture | 31 | 49 | 163 | 326 |
| Refactoring (Fowler) | 30 | 45 | 151 | 303 |
| Refactoring Guru | 27 | 44 | 142 | 284 |
| Release It! | 31 | 50 | 159 | 317 |
| The Pragmatic Programmer | 26 | 42 | 138 | 275 |
| Working Effectively with Legacy Code | 35 | 53 | 178 | 355 |

## Compatibility Summary

- ✅ Complementary: 78 pairs
- ❌ Conflicting: 2 pairs (DDD↔PoEAA, IDDD↔PoEAA)
- 🔁 Overlap: 11 pairs

## Usage Pattern (from USAGE.md)

- Always-on project rule: `mini` (or `nano` if `mini` too large)
- Scoped rule: `mini` or `nano` for path-specific reminders
- On-demand rule: `mini` for refactoring/reviews/migrations
- Skill/Command: usually derived from `mini`
- Retrieval/MCP: `full` or external source material

## Version History

- v0.3 (2026-05-02): Full revalidation, cross-contamination removed
- v0.2 (2026-04-27): `mini`/`nano` naming finalized
- v0.1 (2026-04-16): Initial release with 13 books

## Criticism (from CRITICISM.md)

1. No clear measurement of improvement (validity 9/10) — weakly addressed
2. Token burn / context pollution (validity 9/10) — largely addressed via 3-tier system

## Book Names (short codes)

APoSD, CleanA, CleanC, CodeC, DDIA, DDD, DDD Distilled, IDDD, PoEAA, Refactoring, Ref Guru, Release It, PragProg, WELC