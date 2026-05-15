# ADR-0004: HANDOFF.md two-account rotation system

**Status:** Accepted  
**Date:** 2026-05-13  
**Deciders:** Sid Pathipaka (founder)

## Context

Claude Code sessions have per-account usage limits that force mid-session account switches, sometimes at inconvenient points mid-sprint. Without a deliberate continuity protocol, each new session — whether on a fresh account or resuming after a break — loses all working context: what was completed, what is in progress, what decisions were made during the session, and where the key files are. This context loss is not merely annoying; it causes concrete problems: work is repeated because the agent does not know it was already done, decisions are re-litigated because there is no record they were settled, and 5-15 minutes per session restart are spent reconstructing state that should have been preserved.

The problem is compounded by the two-account rotation: one account runs Opus 4.7 for planning (Phase 1 and 2 of the workflow described in ADR-0003), and a second account runs Sonnet 4.6 for execution (Phase 3). These accounts do not share conversation history, so context must be explicitly serialized to a file that both accounts can read. Relying on Claude's memory features or conversation compaction is insufficient: memory is model-scoped and non-deterministic, and compacted context loses the specific file paths and decision rationale needed to resume work accurately.

A session continuity file (`HANDOFF.md`) at the monorepo root was evaluated against alternatives including: a structured JSON state file (too hard to read and update by hand), a Notion database (requires network, adds latency, complicates offline work), and a GitHub Project board (updates are asynchronous and not readable mid-session). `HANDOFF.md` was chosen because it is a plain text file in the repo, readable and writable by any model without tool calls, committable to git at natural phase boundaries, and structured enough to be parsed quickly in the first 30 seconds of a new session.

## Decision

Maintain `HANDOFF.md` at the monorepo root as the single source of truth for session continuity and two-account rotation state. The file contains the following sections, kept in this order:

1. **Quick orientation** — One paragraph: what the project is, where it lives, and the two-account setup. Readable in 30 seconds.
2. **Last session** — Bullet list of what was completed in the most recent session, with commit hashes where relevant.
3. **Project state** — Tables showing the current status of each active sprint, phase, and repo stub.
4. **Immediate next steps** — An ordered, checkboxed list of the next 3-7 actions. The new session starts here.
5. **Key files** — Reference table: file path, purpose, last modified. Prevents time spent searching for files.
6. **Architecture snapshot** — One paragraph or diagram describing the current system topology. Updated when the architecture changes.
7. **Two-account rotation protocol** — Which account runs which model, how to switch, and the current active account.
8. **Decisions log** — Running append-only list of decisions made, with dates and brief rationale. Prevents re-litigation.

`HANDOFF.md` is committed at the end of every phase (not every task) using a `chore: update HANDOFF.md` commit. New sessions begin with the instruction "read HANDOFF.md and continue." The file is intentionally committed to the repo (not gitignored) because it contains no secrets and its history is useful for auditing sprint execution.

## Consequences

### Positive
- Near-zero context loss across account switches: a new session reading HANDOFF.md is productive within 30 seconds, compared to 5-15 minutes of context reconstruction without it
- Works symmetrically across both accounts, both models (Opus and Sonnet), and any time gap between sessions (hours, days, or weeks)
- The decisions log section prevents re-litigating settled choices, which is a consistent time sink in long-running solo projects where the "founder" may not remember decisions made two weeks prior
- Plain text format means HANDOFF.md is readable without any tool, editable by any agent without special permissions, and diffable in git history
- Committed history of HANDOFF.md snapshots provides a lightweight project journal useful for retrospectives and status reporting

### Negative
- Approximately 30 seconds of overhead per phase boundary to update the file, stage it, and commit; this is non-zero but accepted as the cost of continuity
- If a session ends abruptly (crash, unexpected limit hit mid-phase) without committing, HANDOFF.md will be stale and the next session must reconstruct state partially; this is mitigated by committing at phase boundaries rather than only at session end
- The file grows over time as the decisions log accumulates; periodic pruning is needed to keep the "Quick orientation" and "Immediate next steps" sections scannable; older decisions log entries should be archived to a separate `docs/decisions-log-archive.md`

### Neutral
- `HANDOFF.md` is not gitignored; it is intentionally public within the repo. It contains no secrets (API keys, passwords, PII) because secrets management is handled separately via `.env` files and environment variables, as documented in the security audit
- The two-account rotation protocol section of HANDOFF.md is the canonical record of which account is currently active; this prevents confusion when switching mid-sprint
- The same HANDOFF.md format is used regardless of sprint size; small one-session tasks can have a minimal HANDOFF.md while multi-week sprints have fully populated tables — the structure accommodates both without requiring format changes
