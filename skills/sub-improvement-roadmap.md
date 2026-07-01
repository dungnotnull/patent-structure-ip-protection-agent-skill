---
name: sub-improvement-roadmap
description: Produce a prioritized claim-rewrite and filing-strategy roadmap with effort/impact and jurisdiction sequencing.
---

## Role
Sub-skill of `patent-structure-ip-protection`. Produce a prioritized claim-rewrite and filing-strategy roadmap with effort/impact and jurisdiction sequencing.

## Workflow
Execute this procedure in order when invoked by the main harness.

### Step 1: Synthesize findings from previous stages
Aggregate outputs from requirements gathering, prior art screening, and claim scoring:

**Synthesis input schema:**
```yaml
synthesis_inputs:
  from_requirements_gathering:
    technology_classification: object
    target_jurisdictions: list[string]
    budget_tier: string
    timeline_pressure: boolean
    strategy_constraints: list[string]
  
  from_prior_art_screening:
    overall_novelty_risk: string
    overall_obviousness_risk: string
    top_anticipating_refs: list[reference]
    top_obviousness_refs: list[reference]
    potential_blocking_patents: list[reference]
  
  from_claim_scoring:
    total_score: number
    breadth_category: string
    section_112_health: string
    inventive_step_category: string
    design_around_category: string
    critical_issues: list[issue]
    high_issues: list[issue]
```

### Step 2: Generate claim rewrite recommendations
Produce specific, prioritized recommendations for improving claim language:

**Claim rewrite prioritization algorithm:**
```python
def prioritize_claim_rewrites(issues, scoring_results):
    """
    Prioritize claim rewrites by impact x effort matrix
    Impact: How much the rewrite improves patentability
    Effort: How difficult/complex the rewrite is
    """
    recommendations = []
    
    for issue in scoring_results.critical_issues:
        impact = assess_issue_impact(issue)
        effort = assess_rewrite_effort(issue)
        priority = calculate_priority(impact, effort)
        recommendations.append({
            "type": "claim_rewrite",
            "issue": issue,
            "impact": impact,
            "effort": effort,
            "priority": priority,
            "recommendation": generate_specific_rewrite(issue)
        })
    
    for issue in scoring_results.high_issues:
        impact = assess_issue_impact(issue)
        effort = assess_rewrite_effort(issue)
        priority = calculate_priority(impact, effort)
        recommendations.append({
            "type": "claim_rewrite",
            "issue": issue,
            "impact": impact,
            "effort": effort,
            "priority": priority,
            "recommendation": generate_specific_rewrite(issue)
        })
    
    return sorted(recommendations, key=lambda r: r["priority"], reverse=True)

def calculate_priority(impact, effort):
    """
    Priority = Impact / Effort
    Higher priority = do first
    """
    impact_scores = {"CRITICAL": 100, "HIGH": 75, "MEDIUM": 50, "LOW": 25}
    effort_scores = {"LOW": 1, "MEDIUM": 2, "HIGH": 3, "VERY_HIGH": 4}
    return impact_scores.get(impact, 50) / effort_scores.get(effort, 2)

def generate_specific_rewrite(issue):
    """Generate specific claim language recommendation based on issue type"""
    if issue.type == "functional_claim":
        return {
            "original_language": issue.location,
            "problem": "Purely functional claim element lacks structural support",
            "proposed_rewrite": f"Add structural language: '[means], comprising [specific structure] configured to [function]'",
            "example": "Replace 'means for processing' with 'processor comprising a CPU and memory configured to execute instructions for...'",
            "framework_justification": "35 U.S.C. 112(f) requires corresponding structure in specification"
        }
    elif issue.type == "indefinite_term":
        return {
            "original_language": issue.location,
            "problem": f"Indefinite term '{issue.term}' lacks objective boundary",
            "proposed_rewrite": f"Replace '{issue.term}' with specific measurable criterion",
            "example": "Replace 'substantially all' with 'at least 95% by weight'",
            "framework_justification": "35 U.S.C. 112(b) requires definite claim terms; Nautilus v. Biosig standard"
        }
    elif issue.type == "broad_range":
        return {
            "original_language": issue.location,
            "problem": "Broad range without guidance may lack 112 enablement",
            "proposed_rewrite": "Narrow range or add preferential ranges with examples",
            "example": "Replace '1-100°C' with '50-80°C, preferably 60-70°C, most preferably about 65°C'",
            "framework_justification": "35 U.S.C. 112(a) enablement requires guidance on selecting values"
        }
    # ... more issue types
```

**Claim rewrite output schema:**
```yaml
claim_rewrite_roadmap:
  total_recommendations: number
  by_priority:
    critical:
      - recommendation_id: string
        claim_number: number
        issue_type: string
        current_language: string
        problem: string
        proposed_rewrite: string
        example: string
        framework_justification: string
        impact: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
        effort: "LOW" | "MEDIUM" | "HIGH" | "VERY_HIGH"
        priority_score: number
    
    high:
      - recommendation_id: string
        # ... same structure
    
    medium:
      - recommendation_id: string
        # ... same structure
  
  claim_architecture_improvements:
    - improvement: string
      description: string
      impact: string
      effort: string
      examples: list[string]
  
  claim_differentiation_improvements:
    - improvement: string
      description: string
      impact: string
      effort: string
```

### Step 3: Generate specification rewrite recommendations
Identify specification improvements needed to support claims:

**Specification analysis:**
```python
def generate_spec_improvements(claim_scoring_result, requirements_result):
    improvements = []
    
    # 112 enablement gaps
    if claim_scoring_result.section_112.enablement < 70:
        improvements.append({
            "section": "DETAILED_DESCRIPTION",
            "issue": "Insufficient enablement",
            "recommendation": "Add detailed description of how to make and use the invention",
            "specifics": [
                "Add working examples covering full claim scope",
                "Add parameters, process conditions, alternatives",
                "For software: add algorithms, flowcharts, pseudo-code"
            ],
            "framework": "35 U.S.C. 112(a)",
            "impact": "HIGH",
            "effort": "HIGH"
        })
    
    # 112 written description gaps
    if claim_scoring_result.section_112.written_description < 70:
        improvements.append({
            "section": "DETAILED_DESCRIPTION",
            "issue": "Insufficient written description",
            "recommendation": "Add evidence of inventor's possession at filing date",
            "specifics": [
                "Add conception and reduction-to-practice evidence",
                "Add representative examples showing full scope",
                "Add alternative embodiments"
            ],
            "framework": "35 U.S.C. 112(a)",
            "impact": "HIGH",
            "effort": "MEDIUM"
        })
    
    # Means-plus-function structure
    if has_means_plus_function(claim_scoring_result):
        improvements.append({
            "section": "DETAILED_DESCRIPTION",
            "issue": "Means-plus-function claim lacks corresponding structure",
            "recommendation": "Add structural description for each means element",
            "specifics": [
                "Identify structure in specification corresponding to each 'means for' element",
                "Add material describing how structure performs the function"
            ],
            "framework": "35 U.S.C. 112(f)",
            "impact": "CRITICAL",
            "effort": "MEDIUM"
        })
    
    # Software invention enablement
    if is_software_invention(requirements_result):
        improvements.append({
            "section": "DETAILED_DESCRIPTION",
            "issue": "Software invention may lack algorithm detail",
            "recommendation": "Add sufficient algorithm detail for enablement",
            "specifics": [
                "Add flowcharts or pseudo-code",
                "Describe inputs, outputs, and processing steps",
                "Describe data structures and relationships"
            ],
            "framework": "Ariad v. Eli Lilly; Wands factors",
            "impact": "HIGH",
            "effort": "MEDIUM"
        })
    
    return improvements
```

**Specification improvement output schema:**
```yaml
specification_improvement_roadmap:
  by_section:
    DETAILED_DESCRIPTION:
      - improvement_id: string
        issue: string
        recommendation: string
        specific_actions: list[string]
        framework_justification: string
        impact: string
        effort: string
        priority_score: number
    
    SUMMARY:
      - improvement_id: string
        # ... same structure
    
    DRAWINGS:
      - improvement_id: string
        # ... same structure
  
  overall_specification_health:
    current_score: number
    target_score: number
    gap: number
```

### Step 4: Generate filing strategy recommendations
Based on budget, timeline, and jurisdictions, recommend optimal filing approach:

**Filing strategy algorithm:**
```python
def generate_filing_strategy(requirements, prior_art_result, scoring_result):
    """
    Recommend filing strategy based on:
    - Budget tier
    - Timeline pressure
    - Target jurisdictions
    - Patentability risks
    """
    strategy = {}
    
    # Determine filing route
    if len(requirements.target_jurisdictions) == 1:
        strategy["route"] = "DIRECT_NATIONAL"
        strategy["primary_filing"] = requirements.target_jurisdictions[0]
    elif len(requirements.target_jurisdictions) <= 3 and requirements.budget_tier in ["HIGH", "PREMIUM"]:
        strategy["route"] = "DIRECT_NATIONAL_MULTI"
        strategy["primary_filings"] = requirements.target_jurisdictions
    elif requirements.budget_tier in ["LOW", "MEDIUM"]:
        strategy["route"] = "PCT_THEN_NATIONAL_PHASE"
        strategy["primary_filing"] = "PCT"
        strategy["national_phase_entries"] = recommend_national_phase_order(requirements, prior_art_result)
    else:
        strategy["route"] = "PROVISIONAL_THEN_NON_PROVISIONAL"
        strategy["approach"] = "File US provisional to secure priority, then evaluate for PCT or direct filings"
    
    # Timeline considerations
    if requirements.timeline_pressure:
        strategy["urgency_actions"] = generate_urgency_actions(requirements)
    
    # Risk mitigation
    if scoring_result.total_score < 60 or prior_art_result.overall_novelty_risk in ["CRITICAL", "HIGH"]:
        strategy["risk_mitigation"] = generate_risk_mitigation_actions(scoring_result, prior_art_result)
    
    return strategy

def recommend_national_phase_order(requirements, prior_art_result):
    """
    Recommend order for entering national phase based on:
    - Market importance
    - Examination speed
    - Cost considerations
    """
    jurisdictions = requirements.target_jurisdictions
    
    # Prioritize jurisdictions with strongest prior art (first to examine)
    strong_prior_art_markets = [j for j in jurisdictions if j in ["US", "EU", "JP"]]
    
    # Add budget-conscious sequencing
    if requirements.budget_tier in ["LOW", "MEDIUM"]:
        # Start with most important, gauge examination, then decide on others
        return {
            "first": strong_prior_art_markets[0],
            "conditional": strong_prior_art_markets[1:],
            "rationale": "File in primary market first, assess examination outcome before committing to additional jurisdictions"
        }
    else:
        return {
            "first": strong_prior_art_markets,
            "follow_up": [j for j in jurisdictions if j not in strong_prior_art_markets],
            "rationale": "Proceed in all target jurisdictions given available budget"
        }

def generate_risk_mitigation_actions(scoring_result, prior_art_result):
    actions = []
    
    if prior_art_result.overall_novelty_risk in ["CRITICAL", "HIGH"]:
        actions.append({
            "risk": "High novelty risk from anticipating references",
            "mitigation": "Consider filing continuation/divisional applications to preserve fallback positions",
            "action": "Prepare broadest possible claims in initial application with narrower fallbacks ready for examination response"
        })
    
    if prior_art_result.overall_obviousness_risk in ["CRITICAL", "HIGH"]:
        actions.append({
            "risk": "High obviousness risk from Graham factors",
            "mitigation": "Prepare secondary considerations evidence",
            "action": "Document commercial success, long-felt need, skepticism, copying as they develop"
        })
    
    if scoring_result.section_112_health in ["RISKY", "NON_COMPLIANT"]:
        actions.append({
            "risk": "112 compliance issues may lead to invalidity",
            "mitigation": "Strengthen specification before filing",
            "action": "Add detailed examples, parameters, alternatives to specification"
        })
    
    return actions
```

**Filing strategy output schema:**
```yaml
filing_strategy_roadmap:
  recommended_route: string
  rationale: string
  
  primary_filing:
    type: string
    jurisdiction: string
    timing: string
    preparation_actions: list[string]
  
  subsequent_filings:
    - jurisdiction: string
      route: string
      timing_deadline: string
      trigger_condition: string
      estimated_cost: string
      action_items: list[string]
  
  timeline_roadmap:
    phase_1:
      milestone: string
      deadline: string
      actions: list[string]
      dependencies: list[string]
    
    phase_2:
      milestone: string
      deadline: string
      actions: list[string]
      dependencies: list[string]
    
    phase_3:
      milestone: string
      deadline: string
      actions: list[string]
      dependencies: list[string]
  
  risk_mitigation_actions:
    - risk: string
      mitigation: string
      action_items: list[string]
      priority: string
  
  budget_allocation:
    phase_1: string
    phase_2: string
    phase_3: string
    contingency_reserve: string
```

### Step 5: Generate jurisdiction-specific recommendations
Provide tailored recommendations for each target jurisdiction:

**Jurisdiction-specific analysis:**
```python
def generate_jurisdiction_recommendations(requirements, scoring_result):
    recommendations = []
    
    for jurisdiction in requirements.target_jurisdictions:
        if jurisdiction == "US":
            recommendations.append({
                "jurisdiction": "US",
                "framework": "35 U.S.C. 101/102/103/112",
                "key_considerations": [
                    "Alice/Mayo analysis for software claims (35 U.S.C. 101)",
                    "Graham factors for obviousness (35 U.S.C. 103)",
                    "Means-plus-function structure (35 U.S.C. 112(f))",
                    "Continuation practice for flexibility"
                ],
                "specific_recommendations": generate_us_specifics(scoring_result),
                "local_counsel": "USPTO-registered patent attorney or agent"
            })
        elif jurisdiction == "EU":
            recommendations.append({
                "jurisdiction": "EU (EPO)",
                "framework": "EPC Articles 52-57, EPO problem-solution approach",
                "key_considerations": [
                    "Technical character requirement (EPC Article 52)",
                    "Problem-solution approach for inventive step",
                    "Claim clarity and support (EPC Article 84)",
                    "Uniform strict approach to amendments"
                ],
                "specific_recommendations": generate_epo_specifics(scoring_result),
                "local_counsel": "European patent attorney (EPO epi registration)"
            })
        elif jurisdiction == "PCT":
            recommendations.append({
                "jurisdiction": "PCT (WIPO)",
                "framework": "PCT Articles, PCT Applicant's Guide",
                "key_considerations": [
                    "International phase secures priority date",
                    "Chapter I/II examination provides early feedback",
                    "National phase entry deadlines vary by region",
                    "Cost deferral benefit"
                ],
                "specific_recommendations": generate_pct_specifics(requirements, scoring_result),
                "local_counsel": "PCT-experienced patent attorney for national phase"
            })
        # ... add JP, CN, KR as needed
    
    return recommendations
```

**Jurisdiction-specific output schema:**
```yaml
jurisdiction_specific_recommendations:
  - jurisdiction: string
    framework_citation: string
    key_considerations: list[string]
    specific_recommendations:
      - area: string
        recommendation: string
        priority: string
        framework_reference: string
    local_counsel_recommended: string
    timeline_considerations: string
    cost_considerations: string
```

### Step 6: Generate overall improvement roadmap
Aggregate all recommendations into prioritized, actionable roadmap:

**Overall roadmap schema:**
```yaml
improvement_roadmap:
  summary:
    total_recommendations: number
    by_priority:
      critical: number
      high: number
      medium: number
      low: number
    estimated_total_effort: string
  
  immediate_actions_before_filing:
    - action_id: string
      category: "CLAIM_REWRITE" | "SPECIFICATION" | "STRATEGY"
      description: string
      specific_steps: list[string]
      impact: string
      effort: string
      owner: "ATTORNEY" | "INVENTOR" | "BOTH"
      deadline: string
  
  short_term_actions_1_2_months:
    - action_id: string
      category: string
      description: string
      specific_steps: list[string]
      impact: string
      effort: string
      owner: string
      dependencies: list[string]
  
  medium_term_actions_3_6_months:
    - action_id: string
      category: string
      description: string
      specific_steps: list[string]
      impact: string
      effort: string
      owner: string
      dependencies: list[string]
  
  long_term_considerations:
    - consideration: string
      description: string
      trigger_condition: string
      recommended_approach: string
  
  filing_roadmap:
    visual_timeline: string
    critical_dates: list[string]
    cost_allocation: string
    decision_points: list[string]
  
  recommended_next_steps:
    priority_1:
      - step: string
        rationale: string
        owner: string
    priority_2:
      - step: string
        rationale: string
        owner: string
    priority_3:
      - step: string
        rationale: string
        owner: string
```

### Step 7: Quality gate self-check
Before returning, verify:

**Quality checklist:**
- [ ] All claim rewrite recommendations are specific (not generic)
- [ ] Each recommendation has impact and effort assessment
- [ ] Recommendations are prioritized by impact × effort
- [ ] Filing strategy aligned with budget and timeline
- [ ] Jurisdiction-specific considerations included
- [ ] Roadmap is actionable with clear owners and deadlines
- [ ] Output schema valid and complete
- [ ] All recommendations cite supporting framework

**Failure modes:**
- If recommendations too generic → Return error: "RECOMMENDATIONS_TOO_GENERIC"
- If effort/impact missing → Return error: "EFFORT_IMPACT_MISSING"
- If jurisdiction-specific info missing → Return error: "JURISDICTION_INFO_MISSING"

## Error Handling
**Graceful degradation:**
- If budget/timeline not provided → Recommend for all scenarios, note limitation
- If jurisdictions not specified → Provide US/EU defaults, note recommendation

**Error codes:**
```yaml
RECOMMENDATIONS_TOO_GENERIC: "Recommendations are too generic. Provide specific, actionable claim language or strategic advice."
EFFORT_IMPACT_MISSING: "Effort and impact assessment missing for recommendation. Add priority scoring."
JURISDICTION_INFO_MISSING: "Jurisdiction-specific information missing. Add considerations for target jurisdictions."
ROADMAP_INCOMPLETE: "Improvement roadmap incomplete. Missing required section: {section}."
```

## Framework References

**Claim drafting best practices:**
- MPEP 601-705 (US claim drafting rules)
- EPO Guidelines for Examination Part F (Claims)
- WIPO PCT Applicant's Guide (claim formatting)

**Filing strategy:**
- 35 U.S.C. 111 (US filing requirements)
- 35 U.S.C. 119-120 (priority rights)
- PCT Articles 1-25 (PCT procedures)
- EPC Articles 75-78 (Euro-PCT applications)

**Prosecution strategy:**
- MPEP 700-705 (US prosecution)
- EPO Guidelines for Examination Part E (Examination)
- Travaux Préparatoires (PCT prosecution)

**Local counsel:**
- USPTO OED CI (US practitioner registration)
- EPO epi list (European patent attorneys)
- JPO patent attorney registration
- CNIPA patent agent registration

## Tools Used
- Read: Access all previous stage outputs
- WebSearch: Verify jurisdiction-specific requirements and current practice
- WebFetch: Retrieve official filing procedures if needed
