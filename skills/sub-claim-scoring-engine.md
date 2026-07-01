---
name: sub-claim-scoring-engine
description: Score claim structure for breadth, support (112), inventive step, and design-around resistance against the named frameworks.
---

## Role
Sub-skill of `patent-structure-ip-protection`. Score claim structure for breadth, support (112), inventive step, and design-around resistance against the named frameworks.

## Workflow
Execute this procedure in order when invoked by the main harness.

### Step 1: Parse claim structure
Analyze the claim architecture and identify structural elements:

**Claim parsing:**
```yaml
claim_structure_analysis:
  total_claims: number
  independent_claims: number
  dependent_claims: number
  
  independent_claim_analysis:
    - claim_number: number
      claim_type: "APPARATUS" | "METHOD" | "SYSTEM" | "COMPOSITION" | " COMPUTER-READABLE_MEDIUM"
      preamble: string
      transition_phrase: "comprising" | "consisting of" | "consisting essentially of"
      body_elements: number
      word_count: number
      
      structural_issues:
        - issue_type: string
          location: string
          severity: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
  
  dependent_claim_analysis:
    - claim_number: number
      depends_from: number
      additional_features: number
      single_dependency: boolean
      multiple_dependency: boolean
  
  claim_differentiation:
    breadth_gradient_exists: boolean
    fallback_positions: number
    design_around_resilience_score: number  # 0-100
```

**Transition phrase analysis:**
```yaml
transition_meanings:
  comprising:
    breadth: "BROAD"
    interpretation: "includes listed elements and unspecified equivalents"
    scope_risk: "HIGH"  # may enable validity challenges
  consisting_of:
    breadth: "NARROW"
    interpretation: "includes ONLY listed elements (closed transition)"
    scope_risk: "LOW"  # easier to defend but narrower protection
  consisting_essentially_of:
    breadth: "MEDIUM"
    interpretation: "listed elements plus equivalent variations that don't materially change basic characteristics"
    scope_risk: "MEDIUM"
```

### Step 2: Score claim breadth
Assess how broad or narrow the claims are, considering enforcement and validity risks:

**Breadth scoring algorithm:**
```python
def score_claim_breadth(claim_text, transition, elements):
    # Base score from transition phrase
    base_scores = {"comprising": 80, "consisting essentially of": 50, "consisting of": 20}
    breadth_score = base_scores.get(transition, 50)
    
    # Adjust for element specificity
    specificity = calculate_element_specificity(elements)
    breadth_score -= specificity * 0.3  # More specific = narrower
    
    # Adjust for functional vs structural language
    functional_ratio = count_functional_terms(claim_text) / word_count(claim_text)
    if functional_ratio > 0.3:
        breadth_score += 15  # Functional claims are broader
    
    # Adjust for result-effective vs step-by-step method claims
    if claim_type == "METHOD":
        if "step of" in claim_text.lower():
            breadth_score -= 10  # Step-by-step is narrower
    
    return clamp(breadth_score, 0, 100)

def calculate_element_specificity(elements):
    specificity = 0
    for element in elements:
        # Specific technical parameters = higher specificity
        if re.search(r'\d+(\.\d+)?', element):  # Has numbers
            specificity += 10
        # Brand names or proprietary terms = higher specificity
        if is_propietary_term(element):
            specificity += 15
        # Structural terms = moderate specificity
        if has_structural_terms(element):
            specificity += 5
    return specificity
```

**Output schema (breadth analysis):**
```yaml
breadth_analysis:
  overall_breadth_score: number  # 0-100, higher = broader
  
  independent_claim_scores:
    - claim_number: number
      breadth_score: number
      breadth_category: "ULTRA_BROAD" | "BROAD" | "MODERATE" | "NARROW" | "ULTRA_NARROW"
      transition_impact: string
      
      breadth_contributors:
        - factor: string
          impact: "BROADENING" | "NARROWING"
          magnitude: "HIGH" | "MEDIUM" | "LOW"
      
      breadth_risks:
        - risk: string
          framework: string  # e.g., "35 U.S.C. 103"
          mitigation: string
  
  breadth_tradeoffs:
    advantages_bread: string
    risks_breadth: string
    recommendation: string
```

### Step 3: Score 112 enablement and written description support
Assess whether the specification supports the claims under 35 U.S.C. 112 or EPC Article 83:

**112 analysis (US):**
```python
def analyze_112_compliance(claims, specification):
    enablement_score = 0.0
    description_score = 0.0
    definiteness_score = 0.0
    
    for claim in claims:
        # Enablement: Can a PHOSITA make and use the invention?
        enablement_score += assess_enablement(claim, specification)
        
        # Written description: Did the inventor possess the invention?
        description_score += assess_written_description(claim, specification)
        
        # Definiteness: Are claim boundaries clear?
        definiteness_score += assess_definiteness(claim)
    
    return {
        "enablement": normalize(enablement_score),
        "written_description": normalize(description_score),
        "definiteness": normalize(definiteness_score)
    }

def assess_enablement(claim, spec):
    issues = []
    score = 70.0  # Start with presumption of enablement
    
    # Check for functional claims without sufficient structure
    if has_purely_functional_elements(claim) and not_enabling_structure(spec):
        issues.append("Functional claim lacks corresponding structural disclosure")
        score -= 25
    
    # Check for broad ranges without guidance
    if has_broad_range(claim) and no_range_guidance(spec):
        issues.append("Broad range claim lacks guidance on selecting values within range")
        score -= 20
    
    # Check for prophetic examples only
    if only_prophetic_examples(spec):
        issues.append("Enablement based solely on prophetic examples")
        score -= 15
    
    # Check for software/algorithm claims
    if is_software_claim(claim) and no_algorithm_detail(spec):
        issues.append("Software claim lacks algorithm detail or pseudo-code")
        score -= 20
    
    return score, issues

def assess_definiteness(claim):
    issues = []
    score = 80.0
    
    # Check for ambiguous terms
    ambiguous_terms = ["about", "approximately", "substantially", "plurality", "means for"]
    for term in ambiguous_terms:
        if term in claim.lower():
            if term == "means for" and not "means for" properly_used(claim):
                issues.append(f"'{term}' without corresponding structure in specification")
                score -= 15
            else:
                issues.append(f"'{term}' creates ambiguity")
                score -= 5
    
    # Check for relative terms
    relative_terms = ["high", "low", "strong", "weak", "significant"]
    if any(term in claim.lower() for term in relative_terms):
        issues.append("Relative terms lack objective boundary")
        score -= 10
    
    return score, issues
```

**112 output schema:**
```yaml
section_112_analysis:
  framework_citation: "35 U.S.C. 112(a) (enablement/written description), 35 U.S.C. 112(b) (definiteness)"
  
  enablement:
    score: number  # 0-100
    category: "STRONG" | "ADEQUATE" | "WEAK" | "INADEQUATE"
    issues:
      - issue: string
        location: string
        severity: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
        framework_reference: string
        recommendation: string
  
  written_description:
    score: number
    category: "STRONG" | "ADEQUATE" | "WEAK" | "INADEQUATE"
    issues: list[issue_object]
  
  definiteness:
    score: number
    category: "CLEAR" | "ACCEPTABLE" | "AMBIGUOUS" | "UNCERTAIN"
    issues: list[issue_object]
    indefinite_terms: list[string]
  
  overall_112_health: "COMPLIANT" | "COMPLIANT_WITH_ISSUES" | "RISKY" | "NON_COMPLIANT"
```

**EPC Article 83 analysis (EU):**
```yaml
epc_article_83_analysis:
  framework_citation: "EPC Article 83 (disclosure sufficiency)"
  sufficiency_score: number
  category: "SUFFICIENT" | "BORDERLINE" | "INSUFFICIENT"
  issues:
    - issue: string
      severity: string
      recommendation: string
  reproducibility_assessment:
    can_reproduce: boolean
    missing_information: list[string]
```

### Step 4: Score inventive step / non-obviousness
Assess whether the claims represent an inventive step under EPO or non-obvious under USPTO standards:

**Inventive step analysis (EPO Problem-Solution Approach):**
```python
def analyze_inventive_step_epo(claims, prior_art, closest_prior_art):
    """
    EPO Problem-Solution Approach:
    1. Identify closest prior art
    2. Determine technical problem solved
    3. Assess obviousness of solution
    """
    # Step 1: Closest prior art (already identified)
    
    # Step 2: Distinguishing features
    distinguishing_features = identify_differences(claims, closest_prior_art)
    
    # Step 3: Technical problem
    technical_problem = formulate_technical_problem(distinguishing_features)
    
    # Step 4: Obviousness (Would PHOSITA arrive at solution?)
    obviousness = assess_obviousness_psaproach(
        distinguishing_features,
        technical_problem,
        prior_art
    )
    
    return {
        "closest_prior_art": closest_prior_art,
        "distinguishing_features": distinguishing_features,
        "technical_problem": technical_problem,
        "obviousness_assessment": obviousness
    }

def formulate_technical_problem(features):
    # Problem must be technical (not just improved user experience)
    # Frame as objective technical problem, not advantage
    problems = []
    for feature in features:
        if feature.has_technical_effect():
            problems.append(feature.technical_effect())
    return problems

def assess_obviousness_psaproach(features, problem, prior_art):
    """
    Could-Mount / WOULD-MOUNT Test:
    - Would the PHOSITA combine references to arrive at the solution?
    """
    obviousness_score = 50  # Start neutral
    
    # Check for explicit teaching in prior art
    for ref in prior_art:
        if ref.teaches_combination(features, problem):
            obviousness_score += 30
            break
    
    # Check for structural equivalences
    if has_structural_equivalent(features, prior_art):
        obviousness_score += 20
    
    # Check for predictable results
    if results_are_predictable(features, problem):
        obviousness_score += 15
    
    # Check for known solutions to known problems
    if is_known_solution(features, problem, prior_art):
        obviousness_score += 25
    
    # Inventive concept boosters
    if has_unexpected_result(features):
        obviousness_score -= 20
    if solves_longstanding_problem(features, problem):
        obviousness_score -= 15
    if commercial_success_linked_to_merit(features):
        obviousness_score -= 10
    
    return clamp(obviousness_score, 0, 100)
```

**EPO output schema:**
```yaml
epc_inventive_step_analysis:
  framework_citation: "EPC Articles 52-57, EPO Problem-Solution Approach, EPO Guidelines for Examination Part G"
  
  closest_prior_art:
    reference_id: string
    title: string
    publication_number: string
    rationale: string
  
  distinguishing_features:
    - feature: string
      claim_element: string
      technical_effect: string
      difference_from_prior_art: string
  
  objective_technical_problem:
    problem_statement: string
    technical_field: string
    criteria: "TECHNICAL" | "NON_TECHNICAL"  # Must be technical
  
  obviousness_assessment:
    would_phosita_arrive: boolean
    motivation_to_combine: string
    predictability: "HIGH" | "MEDIUM" | "LOW"
    obviousness_score: number  # 0-100, higher = more obvious
    category: "CLEARLY_INVENTIVE" | "LIKELY_INVENTIVE" | "BORDERLINE" | "LIKELY_OBVIOUS" | "CLEARLY_OBVIOUS"
  
  secondary_considerations:
    unexpected_results: list[string]
    commercial_success: string | null
    long_felt_need: string | null
    skepticism: string | null
```

**Non-obviousness analysis (US Graham factors):**
```yaml
us_nonobviousness_analysis:
  framework_citation: "35 U.S.C. 103, Graham v. John Deere, KSR v. Teleflex"
  
  graham_factors:
    scope_and_content_prior_art:
      assessment: string
      number_references: number
      teaching_away: list[reference_id]
    
    differences_prior_art_claims:
      material_differences: list[string]
      unexpected_results: list[string] | null
    
    level_ordinary_skill:
      determination: string
      evidence: string
    
    secondary_considerations:
      commercial_success: string | null
      long_felt_need: string | null
      skepticism: string | null
      copying: string | null
      licensing: string | null
      praise: string | null
  
  overall_nonobviousness:
    score: number  # 0-100, lower = more obvious
    category: "CLEARLY_NONOBVIOUS" | "LIKELY_NONOBVIOUS" | "BORDERLINE" | "LIKELY_OBVIOUS" | "CLEARLY_OBVIOUS"
    ksr_applications:
      - principle: string  # e.g., "combination of familiar elements"
        application: string
        impact: string
```

### Step 5: Score design-around resistance
Assess how easy it would be for competitors to design around the claims:

**Design-around analysis:**
```python
def analyze_design_around_resistance(claims, prior_art, technology_field):
    """
    Design-around resistance = how easily competitors can achieve same function
    with different structure that doesn't infringe
    """
    resistance_score = 50.0  # Start neutral
    
    # Factors that INCREASE design-around risk (lower score)
    for claim in claims:
        # Purely functional claims = easy to design around
        if is_purely_functional(claim):
            resistance_score -= 25
        
        # Means-plus-function = potentially easy if many equivalents
        if has_means_plus_function(claim):
            resistance_score -= 15
        
        # Single element claims = easy to avoid
        if claim.body_elements <= 1:
            resistance_score -= 20
        
        # Software claims = often easy to work around
        if is_software_claim(claim):
            resistance_score -= 10
    
    # Factors that DECREASE design-around risk (higher score)
    # Multiple, interdependent elements
    if has_interdependent_elements(claims):
        resistance_score += 20
    
    # Essential technical advantage not easily separable
    if has_integrated_advantage(claims):
        resistance_score += 15
    
    # Structural, not functional
    if is_structural_claim(claims[0]):
        resistance_score += 10
    
    # Limited alternative technologies
    if limited_alternatives_technology_field(technology_field):
        resistance_score += 20
    
    return clamp(resistance_score, 0, 100)
```

**Design-around output schema:**
```yaml
design_around_analysis:
  overall_resistance_score: number  # 0-100, higher = harder to design around
  category: "FORTRESS" | "STRONG" | "MODERATE" | "WEAK" | "POROUS"
  
  vulnerability_points:
    - vulnerability: string
      location: string
      severity: "HIGH" | "MEDIUM" | "LOW"
      alternative_approach: string
  
  strengthening_recommendations:
    - recommendation: string
      impact: "HIGH" | "MEDIUM" | "LOW"
      complexity: "HIGH" | "MEDIUM" | "LOW"
  
  claim_fortification_opportunities:
    - opportunity: string
      description: string
      implementation: string
```

### Step 6: Generate multi-dimensional score report
Aggregate all scoring dimensions into comprehensive report:

**Final score schema:**
```yaml
claim_scoring_result:
  overall_assessment:
    total_score: number  # 0-100 weighted composite
    grade: "A" | "B" | "C" | "D" | "F"
    protectability: "HIGH" | "MEDIUM" | "LOW"
    verdict: string
  
  dimensional_scores:
    breadth:
      score: number
      category: string
      framework_citation: string
    
    section_112_compliance:
      enablement_score: number
      written_description_score: number
      definiteness_score: number
      overall_112_health: string
      framework_citation: string
    
    inventive_step_nonobviousness:
      epo_inventive_step_score: number
      us_nonobviousness_score: number
      overall_category: string
      framework_citation: string
    
    design_around_resistance:
      score: number
      category: string
      key_vulnerabilities: list[string]
  
  claim_quality_issues_by_priority:
    critical:
      - issue: string
        claim_number: number
        framework: string
        recommendation: string
    
    high:
      - issue: string
        claim_number: number
        framework: string
        recommendation: string
    
    medium:
      - issue: string
        claim_number: number
        framework: string
        recommendation: string
  
  framework_citations:
    - framework: string
      application: string
      result: string
  
  scoring_methodology:
    breadth_weight: number
    section_112_weight: number
    inventive_step_weight: number
    design_around_weight: number
```

### Step 7: Quality gate self-check
Before returning, verify:

**Quality checklist:**
- [ ] All scoring dimensions grounded in named frameworks
- [ ] Each score has explicit framework citation
- [ ] Breadth analysis considers transition phrases and element specificity
- [ ] 112 analysis covers enablement, written description, and definiteness
- [ ] Inventive step applies EPO problem-solution approach for EU
- [ ] Non-obviousness applies Graham factors for US
- [ ] Design-around analysis identifies concrete vulnerabilities
- [ ] Output schema valid and complete
- [ ] All recommendations are specific (not generic)

**Failure modes:**
- If no claims provided → Return error: "NO_CLAIMS_TO_SCORE"
- If analysis incomplete → Return error: "SCORING_INCOMPLETE"
- If framework citation missing → Return error: "FRAMEWORK_NOT_CITED"

## Error Handling
**Graceful degradation:**
- If specification not provided → Score based on claim text alone, flag limitation
- If prior art not available → Use disclosure for inventive step baseline, flag limitation

**Error codes:**
```yaml
NO_CLAIMS_TO_SCORE: "No claims available for scoring. Please provide patent claims for analysis."
SCORING_INCOMPLETE: "Unable to complete claim scoring. Missing required field: {field_name}."
FRAMEWORK_NOT_CITED: "Scoring dimension not grounded in named framework. Dimension: {dimension}."
SPECIFICATION_MISSING: "Specification not provided. Scoring based on claim text only. 112 analysis may be incomplete."
PRIOR_ART_MISSING: "Prior art not available. Inventive step/non-obviousness assessment may be incomplete."
```

## Framework References
**US frameworks:**
- 35 U.S.C. 101: Patent-eligible subject matter
- 35 U.S.C. 102: Novelty requirements
- 35 U.S.C. 103: Non-obviousness requirements
- 35 U.S.C. 112: Enablement, written description, definiteness
- Graham v. John Deere: Obviousness analysis framework
- KSR v. Teleflex: Obviousness - combinations and common sense
- Alice Corp v. CLS Bank: Abstract ideas exception for software
- Nautilus v. Biosig: Definiteness standard (reasonable certainty)

**EU frameworks:**
- EPC Article 52: Patentable inventions (technical character)
- EPC Article 54: Novelty requirements
- EPC Article 56: Inventive step
- EPC Article 83: Sufficiency of disclosure
- EPO Problem-Solution Approach: Inventive step assessment
- EPO Guidelines for Examination Part G: Patentability

**PCT/WIPO frameworks:**
- PCT Article 33: International preliminary examination
- WIPO Patent Scope: Prior art search

## Tools Used
- Read: Access specification, claims, SECOND-KNOWLEDGE-BRAIN.md
- WebSearch: Verify framework interpretations, search for similar claim language
- WebFetch: Retrieve official framework documents if needed
