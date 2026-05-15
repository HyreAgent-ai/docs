# Architecture Decision Records

This directory contains all architecture decisions for HyreAgent.ai, formatted per Michael Nygard's [ADR pattern](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions).

## Index

| ID | Title | Status | Date |
|---|---|---|---|
| [ADR-0001](ADR-0001-saayam-style-13-repo-structure.md) | Adopt saayam-style 13-repo structure | Accepted | 2026-05-13 |
| [ADR-0002](ADR-0002-apache-2-license-open-core.md) | Apache 2.0 license + open-core posture | Accepted | 2026-05-13 |
| [ADR-0003](ADR-0003-brainstorm-write-plans-subagent-workflow.md) | Brainstorm → Write-plans → Subagents workflow | Accepted | 2026-05-13 |
| [ADR-0004](ADR-0004-handoff-md-two-account-rotation.md) | HANDOFF.md two-account rotation system | Accepted | 2026-05-13 |

## Process

- Number ADRs sequentially. Never re-use a number.
- ADRs are **immutable** once Accepted. New decisions that supersede previous ones get a new ADR that links back.
- Status values: `Proposed` → `Accepted` → `Deprecated` → `Superseded by ADR-NNNN`.
- Each ADR must use the Nygard template (Context, Decision, Consequences with Positive/Negative/Neutral).

## Template

```markdown
# ADR-NNNN: <Short Title>

**Status:** Accepted  
**Date:** YYYY-MM-DD  
**Deciders:** <Name(s)>

## Context

[2-4 paragraphs on the issue, forces at play]

## Decision

[Specific, declarative statement of the choice made]

## Consequences

### Positive
- ...

### Negative
- ...

### Neutral
- ...
```
