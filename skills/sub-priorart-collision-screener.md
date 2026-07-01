---
name: sub-priorart-collision-screener
description: Search and rank prior art (patents + non-patent literature) and estimate 102/103 collision risk against the disclosed claims.
---

## Role
Sub-skill of `patent-structure-ip-protection`. Search and rank prior art (patents + non-patent literature) and estimate 102/103 collision risk against the disclosed claims.

## Workflow
Execute this procedure in order when invoked by the main harness.

### Step 1: Parse claims and extract key features
From the requirements gathering result, identify claim elements and inventive features:

**Claim parsing methodology:**
1. Identify independent claim(s) — typically Claim 1
2. Segment into claim elements (preamble + body + transition phrases)
3. Extract key technical features per element
4. Map features to search keywords

**Output schema (claim analysis):**
```yaml
claim_analysis:
  independent_claims:
    - claim_number: 1
      raw_text: string
      elements:
        - element_id: "e1"
          text: string
          features:
            - feature: string
              keywords: list[str]
              essentiality: "HIGH" | "MEDIUM" | "LOW"
  core_inventive_concept:
    summary: string
    novel_features: list[str]
    technical_field: string
```

### Step 2: Construct search queries
Build targeted search queries for patent and non-patent literature:

**Query construction rules:**
```yaml
search_queries:
  patent_queries:
    - query_type: "broad"
      platforms: ["Google Patents", "USPTO", "Espacenet"]
      template: "{invention_title} AND ({technical_field})"
    - query_type: "feature_specific"
      platforms: ["Google Patents", "Lens.org"]
      template: "({feature_keywords}) AND ({technical_field})"
    - query_type: "combination"
      platforms: ["Google Patents"]
      template: "({feature_A}) AND ({feature_B}) AND ({technical_field})"
  
  npl_queries:
    - query_type: "academic"
      platforms: ["Google Scholar", "Semantic Scholar"]
      template: "{core_concept} {technical_keywords}"
    - query_type: "technical"
      platforms: ["ArXiv", "IEEE Xplore" if available]
      template: "{inventive_feature} AND {application_domain}"
```

**Query optimization:**
- Use Boolean operators: AND, OR, NOT
- Include wildcards for morphological variants
- Add classification filters: IPC/CPC codes from requirements gathering
- Date range: Priority 5 years back, extend to 10 if results sparse

### Step 3: Execute prior art search
Use available tools to search for prior art:

**Search execution:**
```python
def execute_patent_search(query, platforms):
    results = []
    for platform in platforms:
        if platform == "Google Patents":
            results.extend(search_google_patents(query))
        elif platform == "USPTO":
            results.extend(search_uspto(query))
        elif platform == "Espacenet":
            results.extend(search_espacenet(query))
    return deduplicate(results)

def execute_npl_search(query, platforms):
    results = []
    for platform in platforms:
        if platform == "Google Scholar":
            results.extend(search_scholar(query))
        elif platform == "Semantic Scholar":
            results.extend(search_semantic_scholar(query))
        elif platform == "ArXiv":
            results.extend(search_arxiv(query))
    return deduplicate(results)
```

**Tool usage:**
- WebSearch: Primary tool for broad searches
- WebFetch: Retrieve patent full texts when needed
- Read: Access cached results from SECOND-KNOWLEDGE-BRAIN.md

**Graceful degradation:**
- If WebSearch unavailable → Use cached knowledge from SECOND-KNOWLEDGE-BRAIN.md
- If search fails → Log error and continue with partial results
- If zero results → Expand query terms and retry once

### Step 4: Analyze and rank prior art
For each retrieved reference, assess relevance and collision risk:

**Relevance scoring algorithm:**
```python
def relevance_score(reference, claim_elements):
    score = 0.0
    feature_matches = 0
    
    for element in claim_elements:
        for feature in element.features:
            if feature_mentioned_in_reference(feature, reference):
                feature_matches += 1
                score += feature_weight(element.essentiality)
    
    # Adjust for recency and authority
    if reference.publication_date:
        age = calculate_age(reference.publication_date)
        score *= recency_decay(age)
    
    if reference.patent_office in ["USPTO", "EPO"]:
        score *= 1.2  # Authority boost
    
    return normalize(score), feature_matches
```

**Collision risk analysis (per reference):**
```yaml
collision_analysis:
  reference_id: string
  publication_type: "PATENT" | "NPL" | "APPLICATION"
  publication_date: string
  authority: string
  
  relevance_metrics:
    overall_score: number  # 0-100
    element_matches: number
    claimed_elements_disclosed: list[str]
    missing_elements: list[str]
  
  section_102_analysis:
    statutory_basis: string  # "35 U.S.C. 102(a)", "EPC Article 54", etc.
    novelty_risk: "HIGH" | "MEDIUM" | "LOW"
    rationale: string
    anticipating_claims: list[number]
  
  section_103_analysis:
    statutory_basis: string  # "35 U.S.C. 103", "EPC Article 56", etc.
    obviousness_risk: "HIGH" | "MEDIUM" | "LOW"
    graham_factors:
      scope_and_content: string
      differences: list[string]
      ordinary_skill: string
      secondary_indicators: list[string]
    obviousness_rationale: string
```

**Graham v. John Deere factors analysis:**
```yaml
graham_factors_analysis:
  scope_and_content_of_prior_art:
    summary: string
    number_of_references: number
    combined_teachings: string
  
  differences_between_prior_art_and_claims:
    material_differences: list[string]
    unexpected_results: list[string] | null
  
  Level_of_ordinary_skill_in_the_art:
    determined_by: string
    rationale: string
  
  Secondary_considerations:
    commercial_success: boolean | null
    long_felt_but_unsolved_need: boolean | null
    skepticism_of_experts: boolean | null
    copying: boolean | null
    licensing: boolean | null
```

### Step 5: Generate collision risk summary
Aggregate individual reference analyses into overall risk assessment:

**Risk aggregation:**
```python
def aggregate_risk(references):
    max_novelty_risk = max(r.novelty_risk for r in references)
    max_obviousness_risk = max(r.obviousness_risk for r in references)
    
    # Identify most problematic references
    top_anticipating = sorted(
        [r for r in references if r.novelty_risk == "HIGH"],
        key=lambda r: r.relevance_score,
        reverse=True
    )[:3]
    
    top_obviousness = sorted(
        [r for r in references if r.obviousness_risk == "HIGH"],
        key=lambda r: r.relevance_score,
        reverse=True
    )[:3]
    
    return {
        "overall_novelty_risk": max_novelty_risk,
        "overall_obviousness_risk": max_obviousness_risk,
        "top_anticipating_refs": top_anticipating,
        "top_obviousness_refs": top_obviousness
    }
```

**Output schema:**
```yaml
prior_art_screening_result:
  search_parameters:
    queries_executed: number
    platforms_used: list[str]
    date_range: string
    ipc_filters: list[str]
  
  references_found:
    total: number
    by_type:
      patents: number
      applications: number
      npl: number
    by_jurisdiction:
      US: number
      EP: number
      WO: number
      other: number
  
  top_references:
    - reference_id: string
      title: string
      publication_number: string
      publication_date: string
      url: string
      relevance_score: number
      novelty_risk: string
      obviousness_risk: string
      disclosed_features: list[string]
      missing_features: list[string]
  
  collision_risk_assessment:
    section_102_novelty:
      overall_risk: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
      worst_offenders: list[reference_id]
      recommended_actions: list[string]
      framework_citation: string
    
    section_103_obviousness:
      overall_risk: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
      graham_analysis: string
      worst_offenders: list[reference_id]
      recommended_actions: list[string]
      framework_citation: string
    
    freedom_to_operate:
      overall_risk: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW"
      potential_blocking_patents: list[reference_id]
      design_around_opportunities: list[string]
  
  search_limitations:
    note: string
    confidence_level: "HIGH" | "MEDIUM" | "LOW"
```

### Step 6: Quality gate self-check
Before returning, verify:

**Quality checklist:**
- [ ] At least one search query executed OR graceful degradation logged
- [ ] Each reference analyzed for both 102 and 103 risk
- [ ] Graham factors explicitly considered for US jurisdictions
- [ ] EPO problem-solution approach applied for EU jurisdictions
- [ ] Output schema valid and complete
- [ ] All risk assessments cite their governing framework
- [ ] Recommended actions are specific (not generic)

**Failure modes:**
- If search executed but zero results → Return warning: "ZERO_RESULTS" but proceed with disclosure note
- If search unavailable and no cached knowledge → Return error: "SEARCH_UNAVAILABLE"
- If analysis incomplete → Return error: "ANALYSIS_INCOMPLETE"

## Error Handling
**Graceful degradation:**
- If WebSearch fails → Use cached prior art from SECOND-KNOWLEDGE-BRAIN.md if available, set `confidence_level: "LOW"`
- If partial search succeeds → Proceed with available results and flag in `search_limitations`
- If no claims provided from previous stage → Use invention disclosure keywords for search

**Error codes:**
```yaml
ZERO_RESULTS: "No prior art found for search queries. This may indicate truly novel subject matter or overly narrow queries. Consider broadening search terms."
SEARCH_UNAVAILABLE: "Prior art search tools unavailable. Proceeding with disclosure analysis only. Results may miss relevant prior art."
ANALYSIS_INCOMPLETE: "Unable to complete prior art analysis. Missing required field: {field_name}."
CLAIMS_MISSING: "No claims provided for analysis. Using invention disclosure keywords for prior art search."
```

## Framework References
- 35 U.S.C. 102: Anticipation/novelity (US)
- 35 U.S.C. 103: Obviousness (US)
- Graham v. John Deere: Obviousness analysis framework (US Supreme Court)
- EPC Article 54: Novelty (EU)
- EPC Article 56: Inventive step (EU)
- EPO Problem-Solution Approach: Inventive step assessment (EPO)
- KSR v. Teleflex: Obviousness - combination of familiar elements (US Supreme Court)
- Alice Corp v. CLS Bank: Abstract ideas exception (US Supreme Court)

## Tools Used
- WebSearch: Patent and literature searches
- WebFetch: Retrieve full patent documents
- Read: Access SECOND-KNOWLEDGE-BRAIN.md for cached knowledge
