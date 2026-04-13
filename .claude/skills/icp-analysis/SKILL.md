---
name: icp-analysis
description: >
  Performs comprehensive ICP (Ideal Customer Profile) and competitor analysis for any company,
  outputting a professional xlsx spreadsheet with 14 analysis sections. Supports autonomous
  web research, user-provided data, or hybrid mode.
  Use when: user mentions ICP analysis, competitor analysis, customer profiling, target audience
  research, market positioning, core messaging, or wants to analyze a company's ideal customers.
user-invocable: true
---

# ICP & Competitor Analysis Orchestrator

You are a marketing research orchestrator. You coordinate 4 parallel research agents and synthesize their findings into a comprehensive ICP Analysis spreadsheet.

## Arguments

```
/icp-analysis <domain> [--mode auto|manual|hybrid] [--data "notes..."]
```

- `<domain>` (required): The company's domain (e.g., `notion.so`, `figma.com`). The company name is scraped from the website automatically.
- `--mode` (optional, default: `auto`):
  - `auto` - Full autonomous web research. No user input needed.
  - `manual` - User provides all data via `--data` or inline after the command. No web research.
  - `hybrid` - User provides what they know via `--data`, system researches the rest.
- `--data` (optional): Inline notes, context, or partial data the user wants to feed in. Used in `manual` and `hybrid` modes.

**Examples:**
```
/icp-analysis notion.so
/icp-analysis figma.com --mode auto
/icp-analysis acme.com --mode hybrid --data "Our main competitors are FooCorp and BarInc. We sell to enterprise DevOps teams."
/icp-analysis mycompany.com --mode manual --data "B2B SaaS for HR teams. We help companies reduce employee turnover by 40%. Main competitors: BambooHR, Gusto, Rippling. Price: $12/user/mo."
```

## Workflow

### Phase 1: Resolve Company Identity

1. Fetch `https://<domain>` using WebFetch
2. Extract the company name from the page title, og:title meta tag, or prominent heading
3. Confirm to the user: "Analyzing **[Company Name]** (domain). Mode: [auto/manual/hybrid]."
4. Proceed immediately — do NOT ask for confirmation.

If the domain fetch fails, try `https://www.<domain>`. If both fail, search the web for the domain to find the company name.

### Phase 2: Parse User Data (hybrid/manual modes)

If `--data` is provided or mode is `manual`/`hybrid`, parse the user's input for:
- Company/product description
- Target audience details
- Competitor names/URLs
- Pricing info
- USP / differentiators
- Any other marketing context

Structure this as `additional_context` to pass to the relevant agents. In `manual` mode, ALL agents receive this context and skip web research. In `hybrid` mode, agents use this context to supplement web research.

### Phase 3: Parallel Research

Create the research output directory:
```bash
mkdir -p output/research
```

Launch 4 research agents **in parallel** using the Agent tool. Each agent gets:
- The company name and domain
- Instructions to read its specific guide from `references/`
- Instructions to read the schema from `references/section-schemas.md`
- Instructions to write its output JSON to `output/research/`
- The mode (`auto`/`manual`/`hybrid`) and any `additional_context`

#### Agent 1: Prospect Researcher
```
You are a prospect research specialist. Your task is to build a complete prospect profile for [Company Name] (https://[domain]).

MODE: [auto|manual|hybrid]

Read these files for your instructions and output schema:
- references/prospect-guide.md (your research methodology)
- references/section-schemas.md (your output JSON schema - see "prospect-researcher.json" section)

[If auto] Research the company and its target audience using web search and web fetch tools.
[If manual] Use ONLY the provided context below. Do NOT do web research.
[If hybrid] Use the provided context as a starting point, then research to fill gaps.

Write your findings as valid JSON to: output/research/prospect-researcher.json

Company: [name]
Domain: [domain]
User-provided context: [additional_context or "None"]
```

#### Agent 2: Competitor Researcher
```
You are a competitive intelligence specialist. Your task is to find and analyze 6 competitors for [Company Name] (https://[domain]).

MODE: [auto|manual|hybrid]

Read these files for your instructions and output schema:
- references/competitor-guide.md (your research methodology)
- references/section-schemas.md (your output JSON schema - see "competitor-researcher.json" section)

IMPORTANT: Find competitors from a RESULTS perspective - what other ways could a prospect achieve the same result? Not just direct product competitors.

[If auto] Research using web search and web fetch tools.
[If manual] Use ONLY the provided context below. Analyze the named competitors if URLs are given.
[If hybrid] Start with provided competitors, then research to find more and fill gaps.

Write your findings as valid JSON to: output/research/competitor-researcher.json

Company: [name]
Domain: [domain]
User-provided context: [additional_context or "None"]
```

#### Agent 3: Product Analyzer
```
You are a product positioning specialist. Your task is to analyze [Company Name]'s product and develop strategic positioning across 6 frameworks.

MODE: [auto|manual|hybrid]

Read these files for your instructions and output schema:
- references/product-guide.md (your research methodology)
- references/section-schemas.md (your output JSON schema - see "product-analyzer.json" section)

You must develop: product analysis, feature-benefit chains, promise spectrum (5 levels), awareness pyramid (5 levels), irresistible offer, marketing thesis, and Big Idea (EC(PP+UM)+II=BI).

[If auto] Research using web search and web fetch tools.
[If manual] Use ONLY the provided context below.
[If hybrid] Use provided context, then research to fill gaps.

Write your findings as valid JSON to: output/research/product-analyzer.json

Company: [name]
Domain: [domain]
User-provided context: [additional_context or "None"]
```

#### Agent 4: Funnel Strategist
```
You are a marketing funnel strategist. Your task is to develop the execution layer for [Company Name]'s marketing.

MODE: [auto|manual|hybrid]

Read these files for your instructions and output schema:
- references/funnel-guide.md (your research methodology)
- references/section-schemas.md (your output JSON schema - see "funnel-strategist.json" section)

IMPORTANT: Before starting, check if these files exist and read them for context:
- output/research/prospect-researcher.json
- output/research/competitor-researcher.json
- output/research/product-analyzer.json
If they don't exist yet, research the company directly.

You must develop: headlines & lead, full marketing argument, MVF, response flowchart, enhance, and expand strategies.

[If auto] Research using web search and web fetch tools.
[If manual] Use ONLY the provided context below.
[If hybrid] Use provided context, then research to fill gaps.

Write your findings as valid JSON to: output/research/funnel-strategist.json

Company: [name]
Domain: [domain]
User-provided context: [additional_context or "None"]
```

### Phase 4: Completeness Check & Gap Filling

After all 4 agents complete:

1. Read all 4 JSON files from `output/research/`
2. **Scan every field for gaps.** Check every string field in every JSON file. Flag any field that:
   - Is empty, null, or contains only whitespace
   - Contains "Insufficient data", "Not enough data", "manual research recommended", "N/A", or "TBD"
   - Is shorter than 20 characters for fields that should contain substantive analysis (promises, descriptions, benefit statements, etc.)
   - Contains generic placeholder text rather than specific, researched content
3. **If gaps are found, fill them.** For each gap:
   - Do targeted web research yourself (web search + web fetch) to fill the specific missing field
   - Use reasoning and synthesis from the data you DO have to derive fields that are analytical (e.g., Big Idea, thesis, promise spectrum levels can be synthesized from prospect + product data)
   - For competitor fields where a competitor's marketing doesn't publicly disclose data (e.g., pricing), write a specific note like "Not publicly disclosed — contact required for pricing" rather than a generic placeholder
   - Update the JSON file with the filled data
4. **Repeat the scan** after gap-filling. Do NOT proceed to spreadsheet generation until every substantive field has real content.
5. Report the gap-fill results: "Research complete. Filled [N] gaps via follow-up research. Generating spreadsheet..."

### Phase 5: Cross-Reference Validation

After all gaps are filled:

1. Validate cross-references:
   - Does the Big Idea align with the dominant prospect emotion?
   - Do the headline variants reflect the Big Idea?
   - Does the marketing thesis address the biggest gap in competitor positioning?
   - Are the sub-beliefs in logical order?
   - Do the MVF components match the recommended awareness level?
2. Fix any inconsistencies by updating the JSON files

### Phase 5: Spreadsheet Generation

Read `references/xlsx-spec.md` for the exact layout specification.

Use the `anthropic-skills:xlsx` skill to generate the spreadsheet. The skill should:

1. Create a new workbook with 4 sheets: "Prospect Analysis", "Competitor & Product", "Strategy", "Execution"
2. Apply global formatting (Arial font, section headers with yellow background)
3. Populate each section from the corresponding JSON data
4. Apply column widths, text wrapping, merged cells per the spec
5. Save to `output/{CompanyName}_ICP_Analysis_{YYYY-MM-DD}.xlsx`

**Invoke the xlsx skill with a prompt like:**
```
Create a professional xlsx file at output/{CompanyName}_ICP_Analysis_{date}.xlsx

Read the layout specification from references/xlsx-spec.md.
Read the research data from:
- output/research/prospect-researcher.json
- output/research/competitor-researcher.json
- output/research/product-analyzer.json
- output/research/funnel-strategist.json

Create the workbook following the xlsx-spec exactly. Populate all cells from the research JSON.
Use openpyxl for formatting. Apply all styles defined in the spec.
```

### Phase 6: Deliver

Present a summary to the user:

```
## ICP Analysis Complete: [Company Name]

**Output**: output/{CompanyName}_ICP_Analysis_{date}.xlsx

### Key Findings
- **Primary ICP**: [1-2 sentence summary from prospect analysis]
- **Dominant Emotion**: [from W.E.B. analysis]
- **Top Competitor**: [name] - [their positioning]
- **Your USP**: [from product analysis]
- **Big Idea**: [the synthesized Big Idea]
- **Recommended Awareness Level**: [which level to target]
- **Marketing Thesis**: [the one belief]

### Spreadsheet Contents
- Sheet 1: Prospect Analysis (demographics, ideal client, W.E.B., emotions, now/after)
- Sheet 2: Competitor & Product (6 competitors + product features/benefits)
- Sheet 3: Strategy (promise spectrum, awareness pyramid, offer, thesis, big idea)
- Sheet 4: Execution (headlines, marketing argument, MVF, flowchart, enhance, expand)

Would you like me to refine any section?
```

## Error Handling

- If a research agent fails, re-launch it once. If it fails again, do the research yourself inline.
- If web research is blocked (no internet, tool disabled), switch to manual mode and ask the user for the missing data.
- If the xlsx generation fails, save the raw JSON data and offer it as an alternative output.
- **Never deliver a spreadsheet with empty or placeholder cells.** If the gap-filling phase can't fill a field, ask the user for the data before generating the spreadsheet.

## Important Notes

- **No empty cells in the final spreadsheet.** Every cell defined in the xlsx-spec must contain substantive, researched content. The only exception is competitor pricing that is genuinely not public — note "Not publicly disclosed" rather than leaving blank.
- Never fabricate data or invent statistics. But DO synthesize analytical frameworks (thesis, Big Idea, promise spectrum, awareness pyramid, headlines, funnel strategy) from the research data — these are derived analysis, not raw data.
- Competitor data must be captured AS-IS from their marketing — do not editorialize.
- The Big Idea formula (EC(PP+UM)+II=BI) is not optional — it must be properly derived.
- All 14 sections must appear in the final spreadsheet with complete content.
- The final output MUST be an xlsx spreadsheet. JSON files are intermediate artifacts only.
