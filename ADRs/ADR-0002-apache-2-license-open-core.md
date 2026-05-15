# ADR-0002: Apache 2.0 license + open-core posture

**Status:** Accepted  
**Date:** 2026-05-13  
**Deciders:** Sid Pathipaka (founder)

## Context

HyreAgent.ai intends to be open-source-friendly from day one: open infrastructure lowers the barrier for contributors, builds trust with users who can audit what the tool does with their job search data, and creates a community moat that a closed-source competitor cannot easily replicate. At the same time, the project needs commercial flexibility to sustain development — either through a future SaaS tier, enterprise licensing, or founder revenue.

The license choice is a foundational decision because it is effectively irrevocable once a public community forms around a repo. The main options considered were GPL-3.0, AGPL-3.0, MIT, and Apache 2.0.

GPL-3.0 and AGPL-3.0 require derivative works to be released under the same license. AGPL-3.0 closes the "SaaS loophole" by requiring source disclosure even for network-accessed services. Both are incompatible with a future proprietary SaaS tier and would force every enterprise customer evaluating the tool to conduct a GPL license review before adopting it — a common deal-breaker in enterprise procurement.

MIT is maximally permissive but contains no explicit patent grant. A contributor who holds patents on techniques used in their contribution could theoretically assert those patents against users of the software. This is a non-trivial risk for tooling in the AI and automation space, where patent thickets exist.

Apache 2.0 strikes the optimal balance: it is permissive (no copyleft), includes an explicit patent grant from every contributor for their contributions, is compatible with most open-source licenses, and is natively recognized by GitHub's license detection and the Open Source Initiative. It is the dominant license for enterprise-friendly open-source infrastructure (Kubernetes, TensorFlow, Kafka, and the vast majority of CNCF projects use Apache 2.0).

## Decision

Apply Apache 2.0 to all 13 repos in the `HyreAgent-ai` GitHub org. Each repo receives an identical `LICENSE` file generated from the Apache 2.0 template with copyright held by "HyreAgent.ai Contributors."

Adopt an "open-core" posture as the long-term commercial model: all infrastructure, scraping pipelines, tooling, and platform logic is open-source under Apache 2.0; proprietary business logic (billing integrations, enterprise-tier features, managed-service configuration) lives in private branches or a designated private repo when it emerges. The boundary between open and proprietary will be documented in `docs` as a separate ADR when the first paid feature is designed.

No Contributor License Agreement (CLA) is required under Apache 2.0; contributors automatically grant the patent license specified in Section 3 of the license by submitting a pull request.

## Consequences

### Positive
- The explicit patent grant in Apache 2.0 Section 3 protects all users and contributors from patent claims by other contributors — a meaningful protection in AI tooling
- Permissive enough for enterprise adoption without requiring legal review in most organizations; many enterprises have Apache 2.0 on their pre-approved license list
- Compatible with MIT, BSD, and most other permissive licenses, enabling use of the widest possible set of open-source dependencies
- GitHub automatically detects and displays the license, improving discoverability and contributor confidence
- Open-core posture preserves the path to a commercial SaaS tier without license conflict; proprietary features can sit alongside open-source code as long as the boundary is maintained

### Negative
- No copyleft mechanism forces competitors or forks to share improvements back; a well-resourced competitor can take the codebase, add proprietary features, and offer a competing product without contributing back
- The open-core boundary must be actively maintained as a policy decision; if a paid feature accidentally lands in an Apache 2.0 repo, it becomes open-source by publication and cannot be retroactively closed
- License must be reviewed again when the first paid feature is designed; that review should result in ADR-XXXX documenting the open/proprietary boundary

### Neutral
- All 13 repos receive the identical Apache 2.0 `LICENSE` file propagated from the `.github` org template; no per-repo license customization needed at this stage
- CLA is not required; the Apache 2.0 patent grant is implicit in the act of contribution, simplifying the contribution workflow for external contributors
- Copyright line reads "HyreAgent.ai Contributors" rather than "Sid Pathipaka" to avoid requiring copyright assignment as the contributor base grows
