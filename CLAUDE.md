# CLAUDE.md — Patent Structure Analysis & Global IP Protection (USPTO/EPO/WIPO)

**Skill slug:** `patent-structure-ip-protection`
**Source idea:** #169 (Vietnamese backlog `ideas.md`)
**Cluster:** legal-compliance — Legal, Compliance & Governance
**Tagline:** Analyze patent structure, score global protectability, and surface prior-art collision risk.
**Current phase:** Phase 4 — Testing & Validation (initial build complete)

## Problem This Skill Solves
Inventors and SMEs draft patent applications without understanding claim structure, novelty thresholds, or how the same idea will be examined across jurisdictions. Poorly structured claims get rejected, narrowed, or designed around. This skill analyzes a disclosure or draft, scores its protectability against examination standards in the major offices, flags prior-art collision risk, and rewrites claims for breadth and defensibility.

## Harness Flow (Summary)
1. **Intake** → `sub-requirements-gatherer` gathers inputs and frames the problem.
2. **Screen / select** → `sub-priorart-collision-screener` selects the governing framework and screens risk/scope.
3. **Score / analyze** → `sub-claim-scoring-engine` produces a multi-dimensional score against named frameworks.
4. **Knowledge refresh** → optional `tools/knowledge_updater.py` run keeps SECOND-KNOWLEDGE-BRAIN.md current.
5. **Gate** → quality / safety/compliance gates must pass.
6. **Synthesize** → main harness emits the scored deliverable + prioritized improvement roadmap.

## Sub-skills
- `skills/sub-requirements-gatherer.md` — Intake the invention disclosure, target jurisdictions, budget, and commercialization timeline; classify the technology area (IPC/CPC).
- `skills/sub-priorart-collision-screener.md` — Search and rank prior art (patents + non-patent literature) and estimate §102/§103 collision risk against the disclosed claims.
- `skills/sub-claim-scoring-engine.md` — Score claim structure for breadth, support (§112), inventive step, and design-around resistance against the named frameworks.
- `skills/sub-compliance-check.md` — Verify legal-disclaimer posture, confirm no unauthorized practice of law is implied, and gate the deliverable behind a 'consult a registered practitioner' notice.
- `skills/sub-improvement-roadmap.md` — Produce a prioritized claim-rewrite and filing-strategy roadmap with effort/impact and jurisdiction sequencing.

## Tools Required
WebSearch, WebFetch, Read, Write, Bash

## Knowledge Sources (for crawl + reasoning)
- USPTO Patent Public Search (ppubs.uspto.gov) and MPEP
- EPO Espacenet and EPO Guidelines for Examination
- WIPO PATENTSCOPE and PCT Applicant's Guide
- Google Patents and Lens.org for prior-art and citation graphs
- Google Scholar / Semantic Scholar for non-patent literature (NPL)
- ArXiv (cs, eess) for technical prior art in software/hardware inventions

## Supporting Python Tools
- `tools/knowledge_updater.py` — crawl4ai + WebSearch pipeline that fetches latest papers/reports from the domain sources above, scores by recency + relevance, deduplicates by URL/DOI hash, and appends to `SECOND-KNOWLEDGE-BRAIN.md`. Recommended schedule: weekly cron.

## Active Development Tasks
- [x] Scaffold all required deliverables
- [x] Define >=3 sub-skills with quality gates
- [x] Ground scoring in named world-renowned frameworks
- [x] Wire knowledge_updater crawl sources
- [ ] Expand SECOND-KNOWLEDGE-BRAIN with first live crawl batch
- [ ] Add regression fixtures from the test scenarios

## Reference Docs (this folder)
- `PROJECT-detail.md` — full technical spec
- `PROJECT-DEVELOPMENT-PHASE-TRACKING.md` — phase roadmap
- `SECOND-KNOWLEDGE-BRAIN.md` — self-improving knowledge base
- `skills/main.md` — harness entry point
