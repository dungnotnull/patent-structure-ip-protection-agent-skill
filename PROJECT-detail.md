# PROJECT-detail.md — Patent Structure Analysis & Global IP Protection (USPTO/EPO/WIPO)

## Executive Summary
This skill is a full Claude harness that turns analyze patent structure, score global protectability, and surface prior-art collision risk. It operates research-first: every material judgment is grounded in a named, citable framework and, where possible, a freshly retrieved source. It produces a professional-grade deliverable: a multi-dimensional score against the chosen framework plus a prioritized, effort/impact-ranked improvement roadmap.

## Problem Statement
Inventors and SMEs draft patent applications without understanding claim structure, novelty thresholds, or how the same idea will be examined across jurisdictions. Poorly structured claims get rejected, narrowed, or designed around. This skill analyzes a disclosure or draft, scores its protectability against examination standards in the major offices, flags prior-art collision risk, and rewrites claims for breadth and defensibility.

## Target Users & Use Cases
Primary users are practitioners and decision-makers in the **Legal, Compliance & Governance** domain. Trigger examples:
1. User pastes a 1-paragraph invention disclosure for an IoT sensor and asks whether it is patentable in the US and EU.
2. User submits a draft independent claim that is overly broad; skill flags §112 enablement and §101 eligibility risks and rewrites it.
3. Startup wants a low-budget global filing strategy; skill recommends a PCT route with national-phase sequencing.
4. User's idea collides with a 2019 prior-art patent; skill quantifies §103 obviousness risk and proposes narrowing amendments.
5. User asks the skill to 'guarantee' a patent will be granted; compliance gate forces a non-advice disclaimer and recommends a registered practitioner.
6. Software method claim faces Alice/Mayo abstract-idea rejection; skill restructures it toward a technical improvement framing.

## Harness Architecture
```
/patent-structure-ip-protection (main.md harness)
  -> sub-requirements-gatherer              [intake / framing]
  -> sub-priorart-collision-screener              [framework selection / risk-scope screen]
  -> knowledge refresh   [SECOND-KNOWLEDGE-BRAIN via knowledge_updater.py]
  -> sub-claim-scoring-engine              [multi-dimensional scoring]
  -> COMPLIANCE/SAFETY GATE  [mandatory before output]
  -> improvement roadmap [prioritized, effort/impact]
  -> SYNTHESIZE          [final scored deliverable]
```

## Full Sub-Skill Catalog
### sub-requirements-gatherer
- **Purpose:** Intake the invention disclosure, target jurisdictions, budget, and commercialization timeline; classify the technology area (IPC/CPC).
- **Inputs:** outputs of the prior stage + user-provided context.
- **Outputs:** structured findings passed to the next stage.
- **Tools:** WebSearch, WebFetch, Read, Write, Bash
- **Quality gate:** output is schema-valid, evidence-linked, and framework-grounded before the harness proceeds.
### sub-priorart-collision-screener
- **Purpose:** Search and rank prior art (patents + non-patent literature) and estimate §102/§103 collision risk against the disclosed claims.
- **Inputs:** outputs of the prior stage + user-provided context.
- **Outputs:** structured findings passed to the next stage.
- **Tools:** WebSearch, WebFetch, Read, Write, Bash
- **Quality gate:** output is schema-valid, evidence-linked, and framework-grounded before the harness proceeds.
### sub-claim-scoring-engine
- **Purpose:** Score claim structure for breadth, support (§112), inventive step, and design-around resistance against the named frameworks.
- **Inputs:** outputs of the prior stage + user-provided context.
- **Outputs:** structured findings passed to the next stage.
- **Tools:** WebSearch, WebFetch, Read, Write, Bash
- **Quality gate:** output is schema-valid, evidence-linked, and framework-grounded before the harness proceeds.
### sub-compliance-check
- **Purpose:** Verify legal-disclaimer posture, confirm no unauthorized practice of law is implied, and gate the deliverable behind a 'consult a registered practitioner' notice.
- **Inputs:** outputs of the prior stage + user-provided context.
- **Outputs:** structured findings passed to the next stage.
- **Tools:** WebSearch, WebFetch, Read, Write, Bash
- **Quality gate:** output is schema-valid, evidence-linked, and framework-grounded before the harness proceeds.
### sub-improvement-roadmap
- **Purpose:** Produce a prioritized claim-rewrite and filing-strategy roadmap with effort/impact and jurisdiction sequencing.
- **Inputs:** outputs of the prior stage + user-provided context.
- **Outputs:** structured findings passed to the next stage.
- **Tools:** WebSearch, WebFetch, Read, Write, Bash
- **Quality gate:** output is schema-valid, evidence-linked, and framework-grounded before the harness proceeds.

## Skill File Format Specification
Every skill file uses YAML frontmatter (`name`, `description`) followed by the required sections: Role & Persona, Workflow (Harness Flow), Sub-skills Available, Tools, Output Format, Quality Gates. The main harness invokes sub-skills via the Skill tool in the order shown above.

## E2E Execution Flow
1. Parse the user request; if inputs are insufficient, `sub-requirements-gatherer` asks targeted intake questions.
2. `sub-priorart-collision-screener` selects the governing framework(s) and screens scope/risk; branch to a refusal or disclaimer if out of scope.
3. Refresh knowledge if the brain is stale (>7 days) and WebSearch/WebFetch are available; otherwise degrade gracefully to internal knowledge with a stated limitation.
4. `sub-claim-scoring-engine` scores each dimension, citing evidence per claim.
5. Run the safety/compliance gate(s) and a devil's-advocate challenge pass.
6. Emit the scored report + roadmap in the Output Format below.

## SECOND-KNOWLEDGE-BRAIN Integration
- **Sources:** USPTO Patent Public Search (ppubs.uspto.gov) and MPEP; EPO Espacenet and EPO Guidelines for Examination; WIPO PATENTSCOPE and PCT Applicant's Guide; Google Patents and Lens.org for prior-art and citation graphs; Google Scholar / Semantic Scholar for non-patent literature (NPL); ArXiv (cs, eess) for technical prior art in software/hardware inventions
- **Crawl config:** see `tools/knowledge_updater.py` (ArXiv categories cs.GL, eess.SY; domain queries seeded from the idea).
- **Append format:** date-stamped entries with Title, Authors, Year, Venue, DOI/URL, key finding, relevance note; deduplicated by URL/DOI hash.

## Supporting Tools Spec — knowledge_updater.py
- **Inputs:** search queries + source list (in-file config), optional `--since` date.
- **Outputs:** appended entries in `SECOND-KNOWLEDGE-BRAIN.md` + a run log.
- **Schedule:** weekly cron (graceful no-op when offline).

## Quality Gates
- **Compliance gate (mandatory):** run `sub-compliance-check` before emitting the final deliverable; attach a jurisdiction/disclaimer notice and recommend a qualified professional.
- **Evidence gate:** every material claim is traceable to a cited source or a prior step; prefer the highest evidence tier available.
- **Framework gate:** all scoring is grounded in the named frameworks below — never ad-hoc criteria.
- **Challenge gate:** a devil's-advocate pass has stress-tested the recommendation before it is shown.

## Test Scenarios
See `tests/test-scenarios.md` (>=5 concrete scenarios with expected harness behavior).

## Key Design Decisions
1. Framework-grounded scoring only — no ad-hoc rubrics.
2. Research-first with graceful degradation when offline.
3. Composable sub-skills (>=3) so cluster siblings can reuse them.
4. Deliverable is an artifact (scored report + roadmap), not a chat reply.
5. Safety/compliance gate enforced before any sensitive/regulated output.
