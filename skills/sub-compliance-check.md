---
name: sub-compliance-check
description: Verify legal-disclaimer posture, confirm no unauthorized practice of law is implied, and gate the deliverable behind a 'consult a registered practitioner' notice.
---

## Role
Sub-skill of `patent-structure-ip-protection`. Verify legal-disclaimer posture, confirm no unauthorized practice of law is implied, and gate the deliverable behind a 'consult a registered practitioner' notice.

## Workflow
Execute this procedure in order when invoked by the main harness.

### Step 1: Verify scope and jurisdiction appropriateness
Confirm that the request is within the appropriate scope and identify relevant jurisdictions:

**Scope verification:**
```yaml
scope_verification:
  request_type:
    - type: string  # "ANALYSIS", "SCORING", "STRATEGY", "GUARANTEE"
      appropriate: boolean
      reason: string
  
  jurisdiction_verification:
    - jurisdiction: string
      identified: boolean
      framework_applicable: string
      local_counsel_recommended: boolean
      reason: string
```

**Appropriate vs inappropriate requests:**
```yaml
appropriate_requests:
  - "Analyze patentability of this invention in the US"
  - "Score these claims for breadth and 112 compliance"
  - "Identify potential prior art issues"
  - "Recommend claim language improvements"
  - "Assess global filing strategy options"

inappropriate_requests:
  - "Guarantee this patent will be granted"
  - "Provide legal opinion on freedom to operate"
  - "Represent me in patent examination"
  - "File this patent application"
  - "Advise on patent litigation strategy"
```

**Jurisdiction identification:**
- Explicit: User specifies jurisdictions (US, EU, JP, CN, KR, etc.)
- Implicit: Derived from target markets or filing locations
- Default: If not specified, assume US + EU and note assumption

### Step 2: Check for unauthorized practice indicators
Analyze the output and user interaction for indicators of unauthorized practice:

**Unauthorized practice red flags:**
```yaml
unauthorized_practice_check:
  legal_opinion_indicators:
    - indicator: "Binding legal conclusion"
      detected: boolean
      location: string
      severity: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
    
    - indicator: "Attorney-client relationship language"
      detected: boolean
      location: string
      severity: "HIGH"
    
    - indicator: "Representation or advocacy承诺"
      detected: boolean
      location: string
      severity: "CRITICAL"
    
    - indicator: "Guarantee or warranty language"
      detected: boolean
      location: string
      severity: "HIGH"
  
  recommendation_indicators:
    - indicator: "Specific legal strategy without counsel recommendation"
      detected: boolean
      location: string
      severity: "MEDIUM"
    
    - indicator: "Jurisdiction-specific advice without local counsel"
      detected: boolean
      location: string
      severity: "HIGH"
```

**Language patterns to avoid:**
```yaml
prohibited_phrases:
  - "I advise you to..."
  - "You should..."
  "Legal opinion:..."
  - "This constitutes..."
  - "Guaranteed outcome..."
  - "Attorney-client privilege..."
  - "I represent..."
  - "Binding conclusion..."

approved_phrases:
  - "This analysis suggests..."
  - "Consider consulting..."
  - "Framework X indicates..."
  - "Potential risk identified..."
  - "Draft recommendation for attorney review..."
  - "Scored assessment indicates..."
```

### Step 3: Verify disclaimer posture
Ensure that appropriate disclaimers are present and properly positioned:

**Required disclaimer elements:**
```yaml
disclaimer_verification:
  elements_required:
    - element: "Not legal advice"
      present: boolean
      location: string
    
    - element: "Consult qualified professional"
      present: boolean
      location: string
    
    - element: "No attorney-client relationship"
      present: boolean
      location: string
    
    - element: "Jurisdiction limitation"
      present: boolean
      location: string
  
  disclaimer_placement:
    - location: "BEGINNING"
      required: true
      present: boolean
    
    - location: "END"
      required: true
      present: boolean
    
    - location: "CRITICAL_RECOMMENDATIONS"
      required: true
      present: boolean
```

**Standard disclaimer templates:**

**Opening disclaimer:**
```
IMPORTANT DISCLAIMER: This analysis is provided for informational purposes only and does not constitute legal advice or create an attorney-client relationship. Patentability assessment requires evaluation by a qualified patent attorney or agent registered with the relevant jurisdiction(s). This tool identifies potential issues and opportunities based on published frameworks, but cannot guarantee examination outcomes.
```

**Closing disclaimer:**
```
RECOMMENDED NEXT STEPS: Consult with a qualified patent attorney or agent registered with [USPTO for US applications; EPO for EP applications; relevant national offices] to:
1. Review and refine the analysis and recommendations provided
2. Prepare and file patent applications appropriate to your jurisdiction
3. Navigate examination and prosecution procedures
4. Advise on enforcement, licensing, and litigation strategies

The analysis above is based on general frameworks and may not account for jurisdiction-specific nuances or recent legal developments. Your situation may involve factors not considered in this analysis.
```

**High-risk recommendation disclaimers:**
```
NOTE: This recommendation involves [jurisdiction-specific | complex | high-stakes] considerations. Specifically, [factor]. Local counsel familiar with [jurisdiction] patent law should be engaged before proceeding.
```

### Step 4: Verify framework citations
Ensure all material claims cite their governing framework:

**Framework citation verification:**
```yaml
framework_citation_check:
  required_citations:
    - scoring_dimension: string
      required_framework: string
      citation_present: boolean
      citation_location: string
  
  citation_quality:
    - dimension: string
      framework_named: boolean
      specific_provision: boolean  # e.g., "35 U.S.C. 103" not just "US patent law"
      case_law_cited: boolean | null
      examiner_guidance_cited: boolean | null
```

**Required framework mappings:**
```yaml
breadth_scoring:
  required: "Claim differentiation doctrine; transition phrase interpretation"
  citation_format: "Transition phrase '{phrase}' interpreted under [jurisdiction] precedent"

section_112_scoring:
  required: "35 U.S.C. 112(a)-(b) for US; EPC Article 83 for EU"
  citation_format: "35 U.S.C. 112(a) (enablement) / 35 U.S.C. 112(b) (definiteness)"

inventive_step_scoring:
  required: "EPC Article 56 + EPO problem-solution approach for EU; 35 U.S.C. 103 + Graham factors for US"
  citation_format: "EPC Article 56 (inventive step) assessed via EPO problem-solution approach"

nonobviousness_scoring:
  required: "35 U.S.C. 103 + Graham v. John Deere + KSR v. Teleflex"
  citation_format: "35 U.S.C. 103 (non-obviousness) analyzed under Graham v. John Deere factors"
```

### Step 5: Gate compliance
Block deliverable if mandatory compliance elements are missing:

**Compliance gating logic:**
```python
def gate_compliance(verification_result):
    gate_status = "PASS"
    blocking_issues = []
    
    # CRITICAL: Must not appear as legal advice
    if verification_result.unauthorized_practice_indicators.any(severity="CRITICAL"):
        gate_status = "BLOCK"
        blocking_issues.append("Unauthorized practice indicator detected")
    
    # CRITICAL: Must have proper disclaimers
    if not verification_result.disclaimer_verification.all_required_present:
        gate_status = "BLOCK"
        blocking_issues.append("Required disclaimer elements missing")
    
    # HIGH: Must cite frameworks
    if not verification_result.framework_citation_check.all_required_cited:
        gate_status = "CONDITIONAL_PASS"
        blocking_issues.append("Framework citations incomplete - proceed with caution")
    
    # MEDIUM: Should recommend local counsel for non-US jurisdictions
    if verification_result.jurisdiction_verification.has_non_us_without_local_counsel_warning:
        gate_status = "PASS_WITH_RECOMMENDATION"
    
    return {
        "status": gate_status,
        "blocking_issues": blocking_issues,
        "actionable_items": generate_actionable_items(blocking_issues)
    }

def generate_actionable_items(issues):
    items = []
    for issue in issues:
        if "Unauthorized practice" in issue:
            items.append("Remove any language suggesting binding legal advice, representation, or guaranteed outcomes")
        if "Disclaimer" in issue:
            items.append("Add standard disclaimer at beginning and end of deliverable")
        if "Framework citation" in issue:
            items.append("Ensure each scoring dimension cites its governing framework")
    return items
```

### Step 6: Produce compliance result
Return structured compliance assessment to main harness:

**Output schema:**
```yaml
compliance_check_result:
  overall_status: "PASS" | "PASS_WITH_RECOMMENDATIONS" | "CONDITIONAL_PASS" | "BLOCK"
  can_proceed: boolean
  
  scope_verification:
    appropriate: boolean
    jurisdictions_identified: list[string]
    local_counsel_recommended: list[string]
  
  unauthorized_practice_check:
    red_flags_detected: number
    by_severity:
      critical: number
      high: number
      medium: number
    issues:
      - issue: string
        severity: string
        location: string
        recommended_fix: string
  
  disclaimer_verification:
    required_elements_present: boolean
    missing_elements: list[string]
    placement_correct: boolean
  
  framework_citation_check:
    all_required_cited: boolean
    missing_citations: list[string]
  
  disclaimers_to_insert:
    opening: string
    closing: string
    specific_recommendations: list[string]
  
  blocking_issues:
    - issue: string
      fix_required: string
      must_fix_before_proceeding: boolean
  
  recommendations:
    - recommendation: string
      priority: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
      applies_to: string
  
  compliance_notes: list[string]
```

### Step 7: Quality gate self-check
Before returning, verify:

**Quality checklist:**
- [ ] All unauthorized practice indicators checked
- [ ] Disclaimers verified for presence, placement, and content
- [ ] Framework citations verified for all scoring dimensions
- [ ] Jurisdiction identification complete
- [ ] Local counsel recommendations made for non-US jurisdictions
- [ ] Blocking issues clearly identified
- [ ] Actionable fixes provided for any issues
- [ ] Output schema valid and complete

**Failure modes:**
- If gate status is BLOCK → Deliverable cannot proceed
- If critical unauthorized practice indicator → Do not proceed until fixed
- If required disclaimer missing → Do not proceed until added

**Compliance gate failure response:**
```yaml
COMPLIANCE_GATE_BLOCKED:
  message: "Deliverable blocked by compliance gate. Address the following issues before proceeding:"
  issues: list[string]
  action: "Implement fixes and re-run compliance check"
```

## Error Handling
**No graceful degradation for compliance issues.** All compliance elements must pass before deliverable can proceed.

**Error codes:**
```yaml
COMPLIANCE_GATE_BLOCKED: "Deliverable blocked by compliance gate. Critical compliance issues must be addressed."
UNAUTHORIZED_PRACTICE_DETECTED: "Language suggesting unauthorized practice of law detected. Modify language and re-check."
DISCLAIMER_MISSING: "Required disclaimer elements missing. Add disclaimers at specified locations."
FRAMEWORK_CITATION_MISSING: "Scoring dimension not grounded in named framework. Add framework citation."
JURISDICTION_NOT_SPECIFIED: "Target jurisdiction not identified. Specify at least one jurisdiction for analysis."
```

## Compliance Rules Summary

**Do not present output as binding legal/official advice.**
- Use qualifying language: "suggests," "indicates," "potential," "consider"
- Avoid definitive language: "guarantee," "ensure," "will," "must" (except when quoting statutes)

**Identify the governing jurisdiction(s) and flag where they materially differ.**
- Explicitly state which jurisdiction's law applies to each assessment
- Note where jurisdictions have different requirements
- Recommend local counsel for non-US jurisdictions

**Attach a disclaimer recommending a qualified, licensed professional.**
- Opening disclaimer at start of deliverable
- Closing disclaimer with recommended next steps
- Specific disclaimers for high-risk recommendations

**Block the final deliverable if a mandatory compliance element is missing.**
- CRITICAL issues prevent deliverable entirely
- HIGH issues must be addressed before publication
- MEDIUM issues should be addressed with notation

## Framework References

**Unauthorized practice of law:**
- State-specific UPL statutes (US) — vary by jurisdiction
- Legal Services Act 2007 (UK)
- Bundesrechtsanwaltsordnung (Germany)
- Relevant national regulations for each jurisdiction

**Attorney registration:**
- USPTO OED CI practitioner registration
- EPO epi list
- JPO patent attorney registration
- CNIPA patent agent registration

## Tools Used
- Read: Access deliverable for compliance checking
- Write: Insert required disclaimers (main harness responsibility)
