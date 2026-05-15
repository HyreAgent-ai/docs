# ADR-0001: Adopt saayam-style 13-repo structure

**Status:** Accepted  
**Date:** 2026-05-13  
**Deciders:** Sid Pathipaka (founder)

## Context

HyreAgent.ai is a solo project today but is explicitly designed to scale to a 10-person engineering and product team. The current monorepo (`job-pipeline`) has grown organically to mix scraping scripts, serverless API functions, a React SPA, a Chrome extension, and a Flask resume compiler — all in one directory with no clear service boundaries, no standardized labels, and no contributor-facing structure.

The `saayam-for-all` open-source org demonstrates a mature pattern at the opposite end of the scale spectrum: 38 repos, 1,566 volunteers, and GitHub-native project tracking through a "team-as-repo" model where each functional area (engineering, UX, QA, security, program management) has its own repo for issues, documentation, and cross-cutting coordination. This structure scales because ownership is unambiguous and GitHub's native tooling (labels, milestones, issue templates, project boards) works at the repo level.

The question was whether this pattern made sense for HyreAgent at its current scale or whether the overhead of many repos would outweigh the benefits when there is a team of one. A naive copy of saayam's 38-repo structure would be over-engineered. The goal was to find the minimum viable multi-repo structure that supports today's solo work without requiring a painful restructure when the first contributors arrive.

A 13-repo structure was identified as the right-sized answer: one org profile repo, six engineering service repos aligned to the six bounded contexts already visible in the monorepo, and six "team-as-repo" repos for cross-cutting concerns.

## Decision

Adopt a 13-repo structure in the `HyreAgent-ai` GitHub org:

- **1 org profile repo:** `.github` — org-level README, issue templates, label definitions, community health files (CODE_OF_CONDUCT, CONTRIBUTING, SECURITY)
- **6 engineering repos:** `webapp` (React SPA + Chrome extension), `platform` (scraping, orchestration, serverless APIs), `extension` (Chrome extension, if separated later), `resume-compiler` (Flask service), `database` (Supabase migrations, seed data, RLS policies), `infrastructure` (Vercel configs, environment definitions, CI/CD)
- **6 team-as-repo repos:** `docs` (ADRs, architecture, runbooks), `prod` (production incidents, on-call), `proj` (project planning, sprints, roadmap), `ux` (design specs, research), `qa` (test plans, QA checklists), `security` (PRIVATE — vulnerabilities, threat model, audit findings)

This structure is right-sized from saayam's 38 repos. All code remains in the monorepo until a dedicated migration sprint; the new repos start as stubs seeded with README, LICENSE, and standard issue templates.

## Consequences

### Positive
- Clear service ownership: every file has an unambiguous home repo and responsible area
- GitHub-native cross-cutting issue tracking: features that span services get a canonical issue in `proj` with links to child issues in engineering repos
- Standardized labels, milestones, and issue templates propagate from `.github` to all repos automatically via GitHub's community health file inheritance
- Easy contributor onboarding: a contributor to `resume-compiler` does not need to understand the full monorepo; their world is one focused repo
- `security` repo can remain PRIVATE while all other repos are public, protecting in-flight vulnerability disclosures
- Structure matches the target architecture, making migration sprints straightforward rather than requiring a structural redesign under load

### Negative
- More repos to administer than a monorepo; branch protection rules, secrets, and GitHub Actions must be configured per repo
- Cross-repo PRs are awkward for features that genuinely span services; requires discipline to link issues across repos rather than opening one mega-PR
- Admin overhead multiplies with each repo: 13 repos means 13 sets of settings to keep in sync, mitigated partially by the `.github` org-level templates

### Neutral
- Code stays in the existing monorepo until the migration sprint is scheduled; the 13-repo structure is the declared target, not the current reality
- New repos start as stubs — README, LICENSE, and issue templates only — and grow as code migrates
- The structure is a conscious subset of saayam's model, not a direct copy; future repos can be added if new bounded contexts emerge without invalidating this decision
