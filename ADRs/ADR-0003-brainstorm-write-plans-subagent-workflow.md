# ADR-0003: Brainstorm → Write-plans → Subagents workflow

**Status:** Accepted  
**Date:** 2026-05-13  
**Deciders:** Sid Pathipaka (founder)

## Context

Early Claude Code sessions on the HyreAgent.ai groundwork sprint revealed a consistent failure mode: the same model was being used for both strategic planning and mechanical implementation, leading to waste in both directions. Using Opus 4.7 for boilerplate file creation and repo setup burned expensive tokens on tasks that Sonnet 4.6 handles with equal quality. Conversely, using Sonnet 4.6 for architecture decisions and sprint planning produced shallow designs that required costly rework in subsequent sessions.

The root cause is model-task mismatch. Claude model tiers have meaningfully different cost and capability profiles. Opus 4.7 is superior for deep reasoning, novel synthesis, and decisions that require holding many competing constraints in working memory simultaneously. Sonnet 4.6 is fast, cost-efficient, and capable of executing well-specified tasks in parallel with minimal context overhead. Using the wrong model in either direction wastes money or produces lower-quality outputs.

A second structural problem emerged alongside the cost problem: execution without a written plan meant that each Claude Code session had to re-derive what to do next from conversation history, HANDOFF.md, and scattered notes. There was no single machine-readable artifact that told an agent exactly what tasks remained, in what order, with what acceptance criteria. This made session starts slow and made it impossible to safely parallelize work across subagents.

The three-phase workflow was designed to solve both problems simultaneously: match model capability to task type, and produce durable written plans before any code is written.

## Decision

Adopt a three-phase workflow for all future HyreAgent.ai sprints and sessions:

**Phase 1 — Brainstorming (Opus 4.7):** Use the `/brainstorming` skill on Claude Opus 4.7 for ideation, option evaluation, and decision-making. This phase ends with a set of decisions and a rough list of what needs to be built. No code is written; no files are committed. Output: decisions committed to ADRs or HANDOFF.md.

**Phase 2 — Writing Plans (Opus 4.7):** Use the `/writing-plans` skill on Claude Opus 4.7 to turn Phase 1 decisions into a fully-specified implementation plan. Each task in the plan must have: a clear action verb (Create, Write, Configure, Deploy), acceptance criteria, file paths where relevant, and an explicit dependency on other tasks if one exists. Plans are committed to `docs/superpowers/plans/` (or equivalent) before any execution begins. Output: a committed `plan.md` file.

**Phase 3 — Subagent Execution (Sonnet 4.6):** Use the `/subagent-driven-development` skill on Claude Sonnet 4.6 to execute the written plan. Subagents receive the plan file as context and are dispatched to independent task groups in parallel. No strategic decisions are made in this phase; if a blocker requires a decision, execution pauses and the issue is escalated back to Phase 1 in the next session. Output: committed code, pushed branches, verified acceptance criteria.

Plans are committed to version control before execution begins. This ensures that a session interruption at any point in Phase 3 does not lose the plan, and a new session can resume execution by reading the plan file rather than reconstructing intent from conversation history.

## Consequences

### Positive
- Optimal token economics: Opus is used only for tasks where its reasoning advantage is meaningful (planning, decisions); Sonnet handles all execution at a fraction of the cost
- Clean handoff between phases: the committed plan file is a durable artifact that survives session switches, account rotations, and model-tier changes
- Subagents in Phase 3 receive a complete task specification without needing to read all prior conversation context; this enables safe parallelization and faster execution
- Plans accumulate in version control as a historical record of how and why each sprint was executed, supporting future audits and retrospectives
- Discipline to complete planning before execution prevents the common failure mode of starting implementation and discovering mid-way that the architecture is wrong

### Negative
- Requires discipline not to skip Phase 1 or Phase 2 when the task feels "obvious" or small; skipping planning for small tasks is fine, but the threshold must be consciously applied rather than defaulted to always
- Two-account rotation (Sonnet account for execution, Opus account for planning) adds coordination overhead when switching between phases that cross an account limit boundary
- Phase 2 plans can become stale if Phase 1 decisions change after the plan is written; the plan must be updated before Phase 3 execution resumes, requiring a re-read and diff

### Neutral
- The same three-phase pattern is applied to all future sprints; the workflow itself is not task-specific and does not need to be re-decided for each sprint
- Plan files accumulate in `docs/superpowers/plans/` over time and will require periodic archiving to keep the directory navigable; this is acceptable overhead
- The workflow integrates naturally with the HANDOFF.md system (ADR-0004): Phase boundaries are natural commit points, and HANDOFF.md is updated at each phase boundary
