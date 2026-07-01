# tests/test-scenarios.md — Patent Structure Analysis & Global IP Protection (USPTO/EPO/WIPO)

Scenario-based tests for the `patent-structure-ip-protection` harness. Each scenario asserts the harness flow,
framework-grounded scoring, gate enforcement, and deliverable shape.

### Scenario 1
- **Given:** User pastes a 1-paragraph invention disclosure for an IoT sensor and asks whether it is patentable in the US and EU.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.
### Scenario 2
- **Given:** User submits a draft independent claim that is overly broad; skill flags §112 enablement and §101 eligibility risks and rewrites it.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.
### Scenario 3
- **Given:** Startup wants a low-budget global filing strategy; skill recommends a PCT route with national-phase sequencing.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.
### Scenario 4
- **Given:** User's idea collides with a 2019 prior-art patent; skill quantifies §103 obviousness risk and proposes narrowing amendments.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.
### Scenario 5
- **Given:** User asks the skill to 'guarantee' a patent will be granted; compliance gate forces a non-advice disclaimer and recommends a registered practitioner.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.
### Scenario 6
- **Given:** Software method claim faces Alice/Mayo abstract-idea rejection; skill restructures it toward a technical improvement framing.
- **Expected harness behavior:** intake confirms inputs → framework selected → `sub-claim-scoring-engine` scores against the named frameworks → compliance/safety gate enforced with disclaimer → scored report + prioritized roadmap emitted with citations.
- **Pass criteria:** all quality gates pass; every score cites its framework; roadmap items are effort/impact-ranked.

## Cross-cutting checks
- **Graceful degradation:** with WebSearch/WebFetch disabled, the harness still produces a deliverable and explicitly states the knowledge-currency limitation.
- **Refusal/scope:** out-of-scope or unsafe requests are refused or redirected with the mandatory professional-consultation disclaimer.
- **Determinism of structure:** every run yields the six (or seven) Output-Format sections.
