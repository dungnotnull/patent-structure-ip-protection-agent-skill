# Patent Structure Analysis & Global IP Protection

> Analyze patent structure, score global protectability, and surface prior-art collision risk across USPTO, EPO, and WIPO frameworks.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Status: Production Ready](https://img.shields.io/badge/status-production--ready-success.svg)](https://github.com/dungnotnull/patent-structure-ip-protection-agent-skill)

A production-grade AI skill that performs rigorous patentability assessment by analyzing claim structure, scoring protectability against major patent office frameworks, identifying prior-art collision risks, and producing actionable improvement roadmaps.

## Features

### Comprehensive Patentability Analysis
- **Multi-dimensional scoring** across breadth, compliance (35 U.S.C. 112), inventive step, and design-around resistance
- **Framework-grounded analysis** using USPTO (35 U.S.C. 101/102/103/112), EPO (EPC Articles 52-57), and WIPO/PCT standards
- **Prior art screening** with 102/103 collision risk assessment and Graham factor analysis
- **Claim rewrite recommendations** with specific before/after language examples
- **Filing strategy roadmaps** with budget-conscious jurisdiction sequencing

### Production-Grade Architecture
- **5 specialized sub-skills** with typed inputs/outputs and quality gates
- **Graceful degradation** when search tools unavailable
- **Compliance verification** with proper legal disclaimers and unauthorized practice prevention
- **Self-improving knowledge base** that stays current via automated knowledge updates
- **Devil's advocate challenges** that stress-test recommendations before delivery

### Jurisdiction Coverage
- United States (USPTO) - 35 U.S.C. 101/102/103/112 with Graham, Alice, KSR, Nautilus precedents
- Europe (EPO) - EPC Articles 52-57 with problem-solution approach
- International (PCT/WIPO) - PCT filing strategy and national-phase planning
- China, Japan, Korea - Local counsel recommendations with framework awareness

## How It Works

The skill follows a rigorous 7-step harness workflow:

1. **Intake & Framing** - Confirm user goals, gather required inputs, state scope
2. **Framework Selection** - Map jurisdictions to governing legal frameworks
3. **Sub-Skill Execution** - Run 5 specialized analysis sub-skills sequentially
4. **Knowledge Refresh** - Update domain knowledge if stale (optional)
5. **Quality Gates** - Pass evidence, framework, compliance, and challenge gates
6. **Synthesis** - Emit professional report with all findings
7. **Compliance Verification** - Attach disclaimers and recommend qualified counsel

### Sub-Skills

| Sub-Skill | Purpose |
|-----------|---------|
| Requirements Gatherer | Intake disclosure, classify technology (IPC/CPC), assess constraints |
| Prior Art Screener | Search patents/NPL, estimate 102/103 collision risk, rank references |
| Claim Scoring Engine | Score breadth, 112 compliance, inventive step, design-around resistance |
| Compliance Check | Verify disclaimers, prevent unauthorized practice, gate deliverable |
| Improvement Roadmap | Generate claim rewrites and filing strategies with effort/impact ranking |

## Installation

### Prerequisites
- Python 3.8 or higher
- Claude Code or compatible AI harness

### Setup

```bash
# Clone the repository
git clone https://github.com/dungnotnull/patent-structure-ip-protection-agent-skill.git
cd patent-structure-ip-protection-agent-skill

# Optional: Install dependencies for knowledge updater
pip install crawl4ai
```

### Project Structure

```
patent-structure-ip-protection-agent-skill/
├── README.md                          # This file
├── CLAUDE.md                          # Project instructions for Claude
├── PROJECT-detail.md                  # Full technical specification
├── PROJECT-DEVELOPMENT-PHASE-TRACKING.md  # Development phase tracking
├── SECOND-KNOWLEDGE-BRAIN.md          # Self-improving knowledge base
├── skills/
│   ├── main.md                        # Main harness entry point
│   ├── sub-requirements-gatherer.md   # Intake and classification
│   ├── sub-priorart-collision-screener.md  # Prior art search
│   ├── sub-claim-scoring-engine.md    # Multi-dimensional scoring
│   ├── sub-compliance-check.md        # Compliance verification
│   └── sub-improvement-roadmap.md     # Claim rewrite and strategy
├── tools/
│   └── knowledge_updater.py           # Self-improving knowledge pipeline
├── tests/
│   ├── test-scenarios.md              # 6 test scenarios
│   ├── test-fixtures.md               # Regression fixtures
│   └── PHASE4_TESTING_RESULTS.md      # Test validation results
└── docs/
    └── PHASE5_INTEGRATION.md          # Cluster integration patterns
```

## Usage

### Basic Usage

Invoke the skill through Claude Code or compatible harness:

```
Analyze the patentability of my IoT sensor invention for US and EU markets.
```

The skill will guide you through:
1. Providing your invention disclosure
2. Specifying target jurisdictions
3. Sharing budget and timeline constraints
4. Receiving a comprehensive analysis report

### Example Use Cases

**Scenario 1: New Invention Assessment**
```
I've developed a new machine learning algorithm for predicting equipment failure
using vibration and temperature sensors. Is this patentable in the US and Europe?
```

**Scenario 2: Claim Rewrite**
```
Here's my claim: "A method for processing data, comprising: receiving data;
analyzing the data; and outputting a result." How can I strengthen this?
```

**Scenario 3: Filing Strategy**
```
We have $20k budget and want protection in US, EU, and China. What's the best
filing strategy for our software invention?
```

## Output Format

Each analysis produces a professional report with 8 sections:

1. **Executive Summary** - Verdict, headline score, key takeaway
2. **Inputs & Assumptions** - What was provided and assumed
3. **Multi-Dimensional Score** - Scores against named frameworks with citations
4. **Findings** - Strengths, risks, and gaps
5. **Improvement Roadmap** - Prioritized actions ranked by effort × impact
6. **Sources & Limitations** - Citations and graceful-degradation notes
7. **Devil's Advocate Challenges** - Counter-arguments and alternative interpretations
8. **Disclaimer** - Professional consultation notice (mandatory)

## Framework Coverage

### United States (USPTO)
- 35 U.S.C. 101 - Patent-eligible subject matter (Alice Corp v. CLS Bank)
- 35 U.S.C. 102 - Novelty requirements
- 35 U.S.C. 103 - Non-obviousness (Graham v. John Deere, KSR v. Teleflex)
- 35 U.S.C. 112 - Enablement, written description, definiteness (Nautilus v. Biosig)

### Europe (EPO)
- EPC Articles 52-57 - Patentability requirements
- EPO problem-solution approach - Inventive step assessment
- EPO Guidelines for Examination

### International (PCT/WIPO)
- PCT Articles - International filing procedures
- WIPO PCT Applicant's Guide
- WIPO national-phase planning guidelines

## Quality Assurance

### Quality Gates
Every analysis passes through 4 quality gates:

- **Evidence Gate** - All claims traceable to cited sources
- **Framework Gate** - All scoring grounded in named frameworks
- **Compliance Gate** - Proper disclaimers, no unauthorized practice
- **Challenge Gate** - Devil's advocate stress-testing

### Testing
- 6 comprehensive test scenarios covering common use cases
- Regression fixtures for output validation
- Cross-cutting tests for graceful degradation
- Scope refusal tests for inappropriate requests

### Compliance
- Opening and closing disclaimers on all outputs
- Jurisdiction-specific counsel recommendations
- Framework citations prevent ad-hoc advice
- No unauthorized practice of law indicators

## Knowledge Base

The skill maintains a self-improving knowledge base (`SECOND-KNOWLEDGE-BRAIN.md`) that:

- Starts with core frameworks and authoritative sources
- Grows continuously via automated knowledge updates
- Fetches latest papers from ArXiv and domain sources
- Scores entries by recency and relevance
- Deduplicates by URL/DOI hash
- Provides citation-quality references

### Knowledge Update Schedule

Run weekly to stay current:

```bash
# Manual update
python tools/knowledge_updater.py

# Automated weekly cron (recommended)
0 2 * * 0 cd /path/to/skill && python tools/knowledge_updater.py
```

## Cluster Integration

This skill is part of the `legal-compliance` cluster and shares:

- **Compliance Check Schema** - Reusable across legal compliance skills
- **Requirements Gathering Schema** - Shared input validation patterns
- **Standardized Scoring Schema** - Consistent multi-dimensional approach
- **Priority Scoring System** - Unified impact/effort methodology

Siblings can reference shared components without duplication.

## Limitations

### Scope
- **Within scope:** Patentability analysis, claim scoring, prior art screening, filing strategy
- **Outside scope:** Binding legal advice, representation before patent offices, guaranteed outcomes, litigation strategy

### Tool Dependencies
- **WebSearch/WebFetch required** for prior art search and current law verification
- **Graceful degradation** operates with cached knowledge when tools unavailable
- **Confidence reduced** when offline - limitations explicitly stated in output

### Jurisdiction Specificity
- US and EU frameworks fully covered
- PCT/WIPO strategy fully covered
- CN/JP/KR require local counsel - recommendations provided

## Disclaimer

**IMPORTANT:** This skill provides informational analysis only and does not constitute legal advice or create an attorney-client relationship. Patentability assessment requires evaluation by a qualified patent attorney or agent registered with the relevant jurisdiction(s). This tool identifies potential issues and opportunities based on published frameworks, but cannot guarantee examination outcomes.

**Always consult with a qualified patent attorney or agent before:**
- Filing patent applications
- Making legal decisions based on analysis
- Responding to office actions
- Engaging in litigation or enforcement

## Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes with clear documentation
4. Submit a pull request with description

Areas for contribution:
- Additional test scenarios
- Framework updates for legal developments
- Internationalization for non-English jurisdictions
- Integration with additional prior art databases

## License

MIT License - See LICENSE file for details

## Acknowledgments

### Legal Frameworks
- USPTO - United States Patent and Trademark Office
- EPO - European Patent Office
- WIPO - World Intellectual Property Organization
- CNIPA - China National Intellectual Property Administration

### Key Precedents
- Graham v. John Deere (US Supreme Court)
- Alice Corp v. CLS Bank (US Supreme Court)
- KSR v. Teleflex (US Supreme Court)
- Nautilus v. Biosig (US Supreme Court)

### Tools & Libraries
- crawl4ai - Web crawling for knowledge updates
- ArXiv API - Academic paper access
- Claude Code - AI harness framework

## Project Status

**Status:** Production Ready

- All 6 development phases complete
- 5 sub-skills fully implemented
- 6 test scenarios validated
- Quality gates verified
- Documentation comprehensive
- Open source ready

**Version:** 1.0.0  
**Completion Date:** 2026-07-01  
**Phase:** All phases (0-5) 100% complete

## Contact

For questions, issues, or contributions:
- GitHub: [dungnotnull/patent-structure-ip-protection-agent-skill](https://github.com/dungnotnull/patent-structure-ip-protection-agent-skill)
- Issues: [GitHub Issues](https://github.com/dungnotnull/patent-structure-ip-protection-agent-skill/issues)

---

**Made with rigor for patent professionals seeking framework-grounded analysis.**
