---
title: Skill Routing
created: 2026-04-25
updated: 2026-04-25
type: concept
tags: [skill-routing, skill-chain, agentic-coding]
sources: [raw/articles/gstack-skill-routing.md]
confidence: high
---

# Skill Routing

## Definition

Skill routing is the practice of automatically selecting and chaining appropriate skills based on the task's characteristics — purpose, constraints, risks, and requirements.

## The gstack Approach

From the gstack project, skill routing works via CLAUDE.md rules:

```markdown
## Skill routing
When user mentions {trigger}, use {skill} instead of general approach.
```

The system has 20+ specialized skills, each addressing a specific domain.

### Skill Chain Structure

```
/office-hours    → "what to build" clarification
    ↓
/plan-eng-review → architecture planning
    ↓
/ship            → implementation & deployment
    ↓
/qa              → verification & testing
```

## Routing Triggers

Based on PRD characteristics:

| Purpose | Skill |
|---------|-------|
| 습관 형성 | gamification-patterns, habit-psychology |
| 실시간 협업 | websockets-best-practices, crdt-fundamentals |
| 대규모 데이터 | distributed-systems-patterns, streaming-architecture |
| SEO 최적화 | seo-technical-implementation, ssg-vs-ssr |
| 오프라인 지원 | offline-first-architecture, service-worker |
| 보안 중요 | security-review-checklist, auth-implementation |
| 비용 최적화 | cost-optimization, serverless-patterns |

## Context Rot Considerations

When routing for long-running tasks:
- Keep chain length reasonable (each hop adds context)
- Consider [[fresh-context]] at each skill boundary
- Use structured handoff artifacts between skills

## Related

- [[skill-chain]] — execution order of skills
- [[harness-design]] — orchestrating skills in a harness
- [[decomposition]] — sizing units before routing