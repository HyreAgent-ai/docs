# docs

> Architecture decisions, narrative documentation, runbooks, and audit history for HyreAgent.ai.

## What lives here

- Architecture Decision Records (ADRs) in `ADRs/`
- Narrative architecture docs in `docs/`
- Runbooks (incident response, deployment procedures, on-call guides)
- Audit history summaries (high-level write-ups; finding details live in the security repo)
- Wiki content is in this repo's wiki tab: [github.com/HyreAgent-ai/docs/wiki](https://github.com/HyreAgent-ai/docs/wiki)

## What does NOT live here

- Application code (lives in each service repo: platform, webapp, resume-compiler, etc.)
- Issue-level task tracking (use the relevant feature repo or project board)
- Audit finding details and vulnerability evidence (use [security](https://github.com/HyreAgent-ai/security))
- Sprint records and retros (use [proj](https://github.com/HyreAgent-ai/proj))

## How to contribute

1. File an issue (org templates inherit from `.github`)
2. Follow the workflow in [docs wiki → Workflow](https://github.com/HyreAgent-ai/docs/wiki/Workflow)
3. Submit PR to `main`; CI + self-review check; merge after review

## Templates

See `templates/` for starter content. **All template files are placeholders**, marked with `<!-- TEMPLATE - replace with real content when used -->`. Do not treat templates as real data.

## Wiki

This repo's wiki tab is active and is the primary home for process documentation, onboarding guides, and the Hats responsibility map. The wiki tab of all other repos points here.

## Maintainer

Currently solo (@Siddardth7). See [Hats](https://github.com/HyreAgent-ai/docs/wiki/Hats) for current responsibility map.

## License

Apache 2.0 — see [LICENSE](./LICENSE).
