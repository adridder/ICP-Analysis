# Section Schemas

Each subagent must output a JSON file to `output/research/{agent-name}.json` conforming to the schema below. The orchestrator reads these files to populate the final spreadsheet.

---

## prospect-researcher.json

```json
{
  "company_name": "string",
  "demographics": {
    "age_range": "string (e.g., '25-45')",
    "gender_split": "string (e.g., '60% male, 40% female')",
    "income_level": "string",
    "education": "string",
    "geography": "string (primary markets)",
    "industry": "string",
    "job_titles": ["string"],
    "company_size": "string (if B2B)",
    "psychographics": "string (lifestyle, values, interests)",
    "summary": "string (2-3 paragraph demographic profile)"
  },
  "ideal_client": {
    "description": "string (the greatest success story client)",
    "results_achieved": ["string (exact results)"],
    "indirect_transformations": ["string (ripple effects in their life/business)"],
    "profile_summary": "string (1-2 paragraph ideal client portrait)"
  },
  "web_analysis": {
    "wants": {
      "gain": ["string"],
      "be": ["string"],
      "do": ["string"],
      "save": ["string"],
      "avoid": ["string"]
    },
    "emotions": ["string (key emotions prospects experience)"],
    "beliefs": ["string (core beliefs/identifications prospects hold)"]
  },
  "emotional_triggers": {
    "grid": [
      ["string", "string", "string", "string"],
      ["string", "string", "string", "string"],
      ["string", "string", "string", "string"],
      ["string", "string", "string", "string"]
    ]
  },
  "now_after": {
    "have_now": "string",
    "have_after": "string",
    "experience_now": "string",
    "experience_after": "string",
    "status_now": "string",
    "status_after": "string",
    "feel_now": "string",
    "feel_after": "string"
  }
}
```

---

## competitor-researcher.json

```json
{
  "company_name": "string",
  "market_context": "string (what result/outcome prospects are seeking, framed from results-perspective)",
  "threat_assessment": {
    "platform_risk": "string (how likely is it that cloud/AI platforms commoditize this category?)",
    "build_vs_buy_pressure": "string (how hard is it to replicate the core value with DIY?)",
    "category_velocity": "string (how fast is the competitive landscape changing?)"
  },
  "competitors": [
    {
      "number": 1,
      "threat_type": "string (direct | adjacent_platform | build_vs_buy | emerging)",
      "name": "string",
      "hook": "string (their core idea/angle)",
      "primary_marketing_promise": "string",
      "delivery_mechanism": "string (how they deliver the result)",
      "usp": "string (unique selling proposition)",
      "general_marketing_claims": "string (claims made in their marketing)",
      "proof_points": "string (testimonials, case studies, data, certifications)",
      "benefit_statements": "string (key benefits they promote)",
      "deliverables_features": "string (what you actually get)",
      "price_terms": "string (pricing, payment terms, tiers)",
      "bonuses": "string (extras, add-ons, limited-time offers)",
      "risk_reversal": "string (guarantees, refund policies, trials)"
    }
  ]
}
```

Note: `competitors` array must contain exactly 6 entries with the following mix: 2 `direct`, 2 `adjacent_platform`, 1 `build_vs_buy`, 1 `emerging`. If fewer than 6 are found, include the best available and mark remaining as "Not enough data found - manual research recommended."

---

## product-analyzer.json

```json
{
  "company_name": "string",
  "product_analysis": {
    "primary_promise": "string",
    "proof_points": "string",
    "credibility_factors": "string",
    "usp": "string",
    "unique_delivery_mechanism": "string",
    "track_record": "string",
    "performance_metrics": "string",
    "interest_points": "string"
  },
  "features": [
    {
      "name": "string",
      "functional_benefit": "string (makes X easier/faster/better...)",
      "dimensionalized_benefit": "string (...so you can Y every day without Z...)",
      "emotional_benefit": "string (...so you feel P knowing Q)",
      "rank": "number (1-10, higher = more powerful)"
    }
  ],
  "promise_spectrum": [
    {
      "level": 1,
      "label": "Basic",
      "statement": "string (e.g., 'Use our product and get results')"
    },
    {
      "level": 2,
      "label": "Specific",
      "statement": "string (e.g., 'Use our product and get X result in Y days')"
    },
    {
      "level": 3,
      "label": "Mechanism-Named",
      "statement": "string (e.g., 'Use our [named mechanism] and get X result in Y days')"
    },
    {
      "level": 4,
      "label": "Mechanism-Detailed",
      "statement": "string (e.g., 'Use our [detailed mechanism description] and get X result in Y days')"
    },
    {
      "level": 5,
      "label": "Prospect-Experience",
      "statement": "string (leads with prospect's pain/experience, then introduces solution)"
    }
  ],
  "awareness_pyramid": [
    {
      "level": "Most Aware",
      "description": "string (who these prospects are)",
      "messaging_approach": "string (how to talk to them)",
      "example_copy": "string"
    },
    {
      "level": "Product Aware",
      "description": "string",
      "messaging_approach": "string",
      "example_copy": "string"
    },
    {
      "level": "Solution Aware",
      "description": "string",
      "messaging_approach": "string",
      "example_copy": "string"
    },
    {
      "level": "Problem Aware",
      "description": "string",
      "messaging_approach": "string",
      "example_copy": "string"
    },
    {
      "level": "Unaware",
      "description": "string",
      "messaging_approach": "string",
      "example_copy": "string"
    }
  ],
  "irresistible_offer": {
    "deliverables": "string",
    "top_features": ["string (5-7 highest-ranked features with powerful headlines)"],
    "why": "string (why this offer exists)",
    "functional_benefits": "string",
    "dimensionalized_benefits": "string",
    "emotional_benefits": "string"
  },
  "marketing_thesis": {
    "thesis_statement": "string (the one thing the prospect must believe)",
    "thesis_solution": "string (the unique mechanism)",
    "our_thesis": "string (to get X, you need...)",
    "product_connection": "string (to get X, you need our product because...)",
    "argument_order": "string (how thesis, solution, and product connect)"
  },
  "big_idea": {
    "one_emotion": "string",
    "one_promise": "string",
    "one_story": "string",
    "one_thing_to_think": "string",
    "primary_promise_pp": "string",
    "unique_mechanism_um": "string",
    "emotionally_compelling_ec": "string",
    "intellectually_interesting_ii": "string",
    "big_idea_bi": "string (the synthesized Big Idea)",
    "idea_variations": ["string (3-5 alternative expressions of the big idea)"]
  }
}
```

---

## funnel-strategist.json

```json
{
  "company_name": "string",
  "headlines": {
    "dominant_emotion": "string (the one resonant emotion)",
    "lead_copy": "string (the opening lead paragraph/hook)",
    "variants": [
      {
        "headline": "string",
        "subheadline": "string (optional)",
        "ranking": "number (1-10)"
      }
    ]
  },
  "marketing_argument": {
    "sub_beliefs": [
      {
        "belief": "string",
        "rank": "number",
        "claim": "string",
        "proof_1": "string",
        "proof_2": "string",
        "matching_features_benefits": ["string", "string", "string"]
      }
    ],
    "thesis_reminder": "string"
  },
  "mvf": {
    "traffic_source": "string (recommended primary traffic source)",
    "lead_magnet": "string (what to offer)",
    "landing_page_structure": "string (sections/flow of the landing page)",
    "email_sequence_outline": "string (3-5 email sequence overview)",
    "conversion_path": "string (how prospects move from awareness to purchase)",
    "cta": "string (primary call to action)"
  },
  "response_flowchart": {
    "trigger_events": ["string (what triggers each response)"],
    "response_sequences": ["string (what happens at each step)"],
    "decision_points": ["string (where prospects branch)"],
    "follow_up_paths": ["string (what happens for each branch)"]
  },
  "enhance": {
    "ab_test_priorities": ["string (what to test first)"],
    "conversion_optimization_targets": ["string (specific metrics to improve)"],
    "content_upgrades": ["string (content improvements to make)"],
    "audience_expansion": ["string (new audience segments to target)"]
  },
  "expand": {
    "new_channels": ["string (channels to expand into)"],
    "partnership_opportunities": ["string (potential partners)"],
    "scaling_strategy": "string (how to scale what works)",
    "market_expansion": "string (new markets to enter)",
    "product_line_extensions": ["string (new products/services to add)"]
  }
}
```

---

## Notes for Subagents

1. **All string fields should be substantive** - not placeholder text. If research doesn't yield enough data for a field, write "Insufficient data - requires manual research" rather than guessing.
2. **Be specific** - use real numbers, real names, real quotes where found. Generic statements reduce the value of the analysis.
3. **Cite sources** where possible - include "(Source: [URL])" inline when making factual claims based on web research.
4. **Competitor data is captured AS-IS** - record what competitors actually say in their marketing, don't editorialize or interpret.
5. **Features array** should contain 7-12 features, ranked by marketing power (how compelling they are to prospects).
6. **Headlines variants** should contain 8-14 variants, each a distinct angle on the Big Idea.
7. **Sub-beliefs** should contain 6-10 entries, ranked by importance to the overall marketing argument.
