---
name: sub-requirements-gatherer
description: Intake the invention disclosure, target jurisdictions, budget, and commercialization timeline; classify the technology area (IPC/CPC).
---

## Role
Sub-skill of `patent-structure-ip-protection`. Intake the invention disclosure, target jurisdictions, budget, and commercialization timeline; classify the technology area (IPC/CPC).

## Workflow
Execute this procedure in order when invoked by the main harness.

### Step 1: Validate required inputs
Before proceeding, verify the following minimal inputs are available. If any are missing, prompt the user with specific questions:

**Required inputs:**
- **Invention disclosure**: A description of the invention (preferably 1-3 paragraphs, or existing draft claims)
- **Target jurisdictions**: At least one of [US, EU, JP, CN, KR, PCT] or "global"
- **Budget range**: One of [<$10k, $10k-$50k, $50k-$200k, >$200k] or specific amount
- **Commercialization timeline**: Months to first commercial use or filing deadline

**Input validation rules:**
- Disclosure < 50 characters → Request more detail: "Please provide a more detailed description of your invention (at least 2-3 sentences describing what it does and how it works)."
- No jurisdictions specified → Default to [US, EU] and confirm: "No jurisdictions specified. Should I analyze for US and EU markets?"
- Budget unknown → Ask: "What is your approximate budget for patent filing and prosecution? This helps recommend appropriate filing strategies."
- Timeline unknown → Ask: "When do you plan to first commercialize or publicly disclose this invention? This affects filing urgency."

### Step 2: Classify technology area (IPC/CPC)
Use the invention disclosure to identify applicable IPC/CPC classifications:

**Classification methodology:**
1. Identify technical field keywords from the disclosure (e.g., "IoT sensor" → H04L, G01S; "software method" → G06F)
2. Map to primary IPC sections using IPC scheme:
   - A: Human necessities
   - B: Performing operations; transporting
   - C: Chemistry; metallurgy
   - D: Textiles; paper
   - E: Fixed constructions
   - F: Mechanical engineering; lighting; heating; weapons; blasting
   - G: Physics
   - H: Electricity
3. If WebSearch available, verify classification: `WebSearch query="{disclosure keywords} IPC CPC classification"`
4. Return primary IPC subclass + example CPC subclass

**Output schema:**
```yaml
technology_classification:
  primary_ipc: "G06F"  # Example
  cpc_examples: ["G06F 3/00", "G06F 9/00"]
  technical_field: "Computing; calculating"
  keywords: ["software", "algorithm", "data processing"]
  classification_method: "keyword_mapping" # or "web_search_verified"
```

### Step 3: Map jurisdictions to governing frameworks
For each target jurisdiction, map to the applicable examination frameworks:

**Framework mapping:**
```yaml
framework_mapping:
  US:
    statutes: ["35 U.S.C. 101", "35 U.S.C. 102", "35 U.S.C. 103", "35 U.S.C. 112"]
    case_law: ["Graham v. John Deere", "Alice Corp v. CLS Bank", "KSR v. Teleflex"]
    examiner_guidance: "MPEP (Manual of Patent Examining Procedure)"
  EU:
    conventions: ["EPC Articles 52-57"]
    approach: "EPO problem-solution approach for inventive step"
    examiner_guidance: "EPO Guidelines for Examination"
  PCT:
    treaty: "Patent Cooperation Treaty"
    guidance: "PCT Applicant's Guide, WIPO national-phase planning"
  JP/CN/KR:
    note: "National-specific requirements; recommend verifying with local counsel"
```

### Step 4: Assess filing constraints
Based on budget and timeline, identify filing strategy constraints:

**Budget-tier analysis:**
```yaml
budget_analysis:
  tier: "LOW" | "MEDIUM" | "HIGH" | "PREMIUM"
  constraints:
    - If tier == "LOW": Recommend narrow geographic scope (1-2 jurisdictions), consider provisional then non-provisional
    - If tier == "MEDIUM": Consider PCT route for 3-5 jurisdictions
    - If tier == "HIGH": Direct filings in major markets + PCT for broader coverage
    - If tier == "PREMIUM": Comprehensive global coverage including JP/CN/KR
  timeline_pressure: boolean  # true if < 6 months to disclosure/deadline
```

**Timeline urgency scoring:**
```python
urgency_score = {
    "immediate": 0,  # Already disclosed or deadline < 1 month
    "high": 1,       # 1-3 months
    "medium": 2,     # 3-6 months
    "low": 3         # > 6 months
}
```

### Step 5: Produce structured output
Return a structured result object to the next stage:

**Output schema:**
```yaml
requirements_gathering_result:
  inputs_confirmed:
    invention_disclosure: string
    target_jurisdictions: list[str]
    budget_range: string
    commercialization_timeline: string
    user_provided_claims: list[str] | null
  
  technology_classification:
    primary_ipc: string
    cpc_examples: list[str]
    technical_field: string
    keywords: list[str]
    classification_method: string
  
  framework_mapping:
    jurisdictions:
      - jurisdiction: string
        statutes: list[str]
        case_law: list[str] | null
        examiner_guidance: string
    recommended_frameworks: list[str]
  
  filing_constraints:
    budget_tier: string
    timeline_pressure: boolean
    urgency_score: number
    strategy_constraints: list[str]
  
  quality_indicators:
    disclosure_quality: "HIGH" | "MEDIUM" | "LOW"
    classification_confidence: "HIGH" | "MEDIUM" | "LOW"
    completeness: "COMPLETE" | "PARTIAL" | "INCOMPLETE"
```

### Step 6: Quality gate self-check
Before returning, verify:

**Quality checklist:**
- [ ] All required inputs collected or explicitly confirmed as missing
- [ ] Technology classification grounded in IPC scheme (not guessed)
- [ ] Each jurisdiction mapped to at least one named framework
- [ ] Budget and timeline analyzed for strategy constraints
- [ ] Output schema valid and complete
- [ ] No assumptions asserted without basis (e.g., if user didn't provide claims, state "no claims provided" rather than inferring)

**Failure modes:**
- If disclosure is too vague (< 100 chars) → Return error: "DISCLOSURE_INSUFFICIENT"
- If no jurisdictions can be inferred → Return error: "JURISDICTIONS_MISSING"
- If any required output field is null → Return error: "OUTPUT_INCOMPLETE"

## Error Handling
**Graceful degradation:**
- If WebSearch fails during classification → Use keyword-based classification and set `classification_method: "keyword_fallback"` with note in output
- If jurisdiction unknown → Include in framework_mapping as `"note: verify with local counsel"`

**Error codes:**
```yaml
DISCLOSURE_INSUFFICIENT: "Invention disclosure too brief for analysis. Please provide at least 2-3 sentences describing what the invention does and how it works."
JURISDICTIONS_MISSING: "No target jurisdictions specified. Please specify at least one jurisdiction (US, EU, JP, CN, KR, or PCT)."
OUTPUT_INCOMPLETE: "Internal error: unable to complete requirements gathering. Missing required field: {field_name}."
```

## Tools Used
- WebSearch: For verifying IPC/CPC classification and jurisdiction-specific requirements
- WebFetch: For retrieving official classification schemes if needed
- Read: For accessing SECOND-KNOWLEDGE-BRAIN.md for framework reference

## Framework References
- IPC scheme: WIPO International Patent Classification
- CPC: EPO Cooperative Patent Classification
- MPEP: USPTO Manual of Patent Examining Procedure
- EPO Guidelines: EPO Guidelines for Examination
