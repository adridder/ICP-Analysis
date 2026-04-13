# ICP Analysis - Spreadsheet Specification

This document defines the exact structure, formatting, and content layout for the generated ICP Analysis xlsx report. The orchestrator reads this spec when generating the final spreadsheet via the `xlsx` skill.

## Global Formatting

- **Font**: Arial, 11pt default
- **Section Headers**: Arial, 14pt, bold, yellow background (#FFFF00), dark text
- **Sub-headers**: Arial, 13pt, bold
- **Body text**: Arial, 11pt
- **Text wrapping**: Enabled for all content cells
- **Grid lines**: Visible

## Sheet Structure

The workbook contains **4 sheets** (re-organized from the original single-sheet template for clarity):

### Sheet 1: "Prospect Analysis" (Sections 1)
### Sheet 2: "Competitor & Product" (Sections 2-3)
### Sheet 3: "Strategy" (Sections 4-8)
### Sheet 4: "Execution" (Sections 9-14)

---

## Sheet 1: Prospect Analysis

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Row labels (A-E) |
| B | 22 | Sub-section titles |
| C | 25 | Prompt/question text |
| D-J | 20 | Response content |

### Section 1A: Demographics (Rows 2-16)
- **B2**: Section header "1. Prospect Analysis" (merged B2:J2, yellow bg, 14pt bold)
- **A4**: "A"
- **B4**: "Demographics" (bold)
- **C4:J16**: Merged area for demographic data
  - Content populated from `prospect-researcher` output field: `demographics`
  - Include: age range, gender split, income level, education, geography, industry, job titles, company size

### Section 1B: Ideal Client Targeting (Rows 18-20)
- **A18**: "B"
- **B18**: "Ideal Client Targeting" (bold)
- **C18:E18**: Merged - prompt text: "Imagine the client that is your greatest success story. What results did they get? What transformations happened?"
- **C19:J20**: Merged area for ideal client description
  - Content from `prospect-researcher` output field: `ideal_client`

### Section 1C: W.E.B. Analysis (Rows 22-32)
- **A22**: "C"
- **B22:B28**: Merged - "Prospect W.E.B. Analysis: Wants, Emotions, Beliefs"
- **C22:C28**: Merged - prompt questions
- **D22:J32**: Merged area for W.E.B. content
  - Content from `prospect-researcher` output field: `web_analysis`
  - Structure as:
    - Wants to gain...
    - Wants to be...
    - Wants to do...
    - Wants to save...
    - Wants to avoid...
    - Key emotions
    - Core beliefs/identifications

### Section 1D: Emotional Triggers (Rows 34-37)
- **A34**: "D"
- **B34**: "Prospect Emotional Triggers" (bold)
- **C34:C36**: Merged - "Prospect wants to buy/avoid which feelings?"
- **D34:G37**: 4x4 grid of emotional triggers
  - Content from `prospect-researcher` output field: `emotional_triggers`
  - Layout: 4 columns x 4 rows of feeling words (e.g., inadequacy, greed, envy, fear, belonging, pride, etc.)

### Section 1E: Now/After Contrasting (Rows 39-46)
- **A39**: "E"
- **B39:B46**: Merged - "Now/After Contrasting"
- **C39:F44**: Merged - prompt text explaining the now/after exercise
- **D46:I46**: Merged - contrasting content
  - Content from `prospect-researcher` output field: `now_after`
  - Structure as:
    - Have Now: / Have After:
    - Avg Experience Now: / Avg Experience After:
    - Status Now: / Status After:
    - Feel Now: / Feel After:

---

## Sheet 2: Competitor & Product

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 8 | Number |
| C | 20 | Name |
| D | 25 | Hook (Idea) |
| E | 30 | Primary Marketing Promise |
| F | 20 | Delivery Mechanism |
| G | 25 | USP |
| H | 30 | General Marketing Claims |
| I | 25 | Proof Points |
| J | 25 | Benefit Statements |
| K | 25 | Deliverables/Features |
| L | 15 | Price/Terms |
| M | 20 | Bonuses |
| N | 20 | Risk-Reversal |

### Section 2: Competitor Analysis (Rows 2-12)
- **B2**: Section header "2. Competitor Analysis" (merged B2:N2, yellow bg, 14pt bold)
- **B4:N4**: Instruction text: "TOP Competitors (Results-perspective)"
- **B6:N6**: Column headers (13 columns, bold, 13pt):
  `Number | Name | Hook (Idea) | Primary Marketing Promise | Delivery Mechanism | USP | General Marketing Claims | Proof Points | Benefit Statements | Deliverables/Features | Price/Terms | Bonuses | Risk-Reversal`
- **B7:N12**: 6 data rows (one per competitor)
  - Content from `competitor-researcher` output field: `competitors[]`
  - Each competitor is an object with all 13 fields

### Section 3: Product Analysis (Rows 16-40)
- **B16**: Section header "3. Our Product Analysis" (merged B16:N16, yellow bg, 14pt bold)
- **B18**: "Primary Promise" | **C18**: value
- **B19**: "Proof Points" | **C19**: value
- **B20**: "Credibility Factors" | **C20**: value
- **B21**: "USP" | **C21**: value
- **B22**: "Unique Delivery Mechanism" | **C22**: value
- **B23**: "Track Record" | **C23**: value
- **B24**: "Performance Metrics" | **C24**: value
- **B25**: "Interest Points" | **C25**: value
  - Content from `product-analyzer` output field: `product_analysis`

#### Features & Benefits Sub-table (Rows 28-40)
- **B27**: "Features" (bold) | **C27**: "Benefits" (bold)
- **B28**: "Feature" | **C28**: "Functional Benefits" | **D28**: "Dimensionalized Benefit" | **E28**: "Emotional Benefit" | **F28**: "Rank"
- **B29:F40**: Up to 12 feature rows
  - Content from `product-analyzer` output field: `features[]`
  - Each feature has: name, functional_benefit, dimensionalized_benefit, emotional_benefit, rank

---

## Sheet 3: Strategy

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 25 | Labels |
| C-H | 25 | Content |
| I | 40 | Notes/guidance |

### Section 4: Promise Exposure Spectrum (Rows 2-20)
- **B2**: Section header "4. Promise Exposure Spectrum" (merged B2:H2, yellow bg, 14pt bold)
- **B4**: "Develop the Promise" (bold)
- **B6:H6**: Level headers: "Level 1 (Basic)" through "Level 5 (Sophisticated)"
- **B7:H11**: 5 promise levels, each with:
  - The promise statement at that level
  - Content from `product-analyzer` output field: `promise_spectrum[]`
  - Level 1: Basic product + benefit
  - Level 2: Product + specific measurable result
  - Level 3: Named mechanism + specific result
  - Level 4: Detailed mechanism + specific result
  - Level 5: Prospect-experience-led messaging

### Section 5: Prospect Awareness Pyramid (Rows 22-42)
- **B22**: Section header "5. Prospect Awareness Pyramid" (merged B22:H22, yellow bg, 14pt bold)
- **B24**: "Talk to the prospect at their awareness level" (bold)
- **B26:H38**: 5 awareness levels with messaging approach:
  - Content from `product-analyzer` output field: `awareness_pyramid`
  - Most Aware → Product Aware → Solution Aware → Problem Aware → Unaware
  - For each: description, recommended messaging approach, example copy

### Section 6: Superior & Irresistible Offer (Rows 44-56)
- **B44**: Section header "6. Superior & Irresistible Offer" (merged B44:H44, yellow bg, 14pt bold)
- **B46**: "Deliverables" | **C46**: value
- **B47**: "Features" | **C47**: "(top 5-7 from section 3, with powerful headline)"
- **B48:C48**: Top 5-7 features listed
- **B50**: "Why" | **C50**: value
- **B51**: "Functional Benefits" | **C51**: value
- **B52**: "Dimensionalized Benefits" | **C52**: value
- **B53**: "Emotional Benefits" | **C53**: value
  - Content from `product-analyzer` output field: `irresistible_offer`

### Section 7: Marketing Thesis (Rows 58-82)
- **B58**: Section header "7. Marketing Thesis" (merged B58:H58, yellow bg, 14pt bold)
- **B60:H72**: Thesis development area
  - Content from `product-analyzer` output field: `marketing_thesis`
- **B74**: "Thesis:" | **C74:H78**: The thesis statement
- **B80**: "Marketing Argument Order"
- **B81**: "1. Thesis Solution" | **C81**: "2. Our Marketing Thesis" | **D81**: "3. Product"
- **B82**: "(a unique mechanism)" | **C82**: "To get X, you need:" | **D82**: "To get X, you need:"

### Section 8: The Big Idea (Rows 84-110)
- **B84**: Section header "8. The Big Idea" (merged B84:H84, yellow bg, 14pt bold)
- **B86**: "1 emotion" | **C86**: value
- **B87**: "1 promise" | **C87**: value
- **B88**: "1 story" | **C88**: value
- **B89**: "1 thing to think" | **C89**: value
- **B91**: "Primary Promise (PP)" | **C91**: value
- **B92**: "and"
- **B93**: "Unique Mechanism (UM)" | **C93**: value
- **B95**: "Emotionally Compelling (EC)" | **C95**: value
- **B96**: "Intellectually Interesting (II)" | **C96**: value
- **B97**: "Big Idea (BI)" | **C97**: value
- **B99**: "EC (PP + UM) + II = BI" (bold, merged B99:C99)
- **I86**: "Overcome this being something they've seen before to prevent mental opt-out"
- **I87**: "The Big Idea is a pattern-interrupt"
- **I88**: "The 'ad' for the funnel, NOT the offer...the big idea is the hook"
  - Content from `product-analyzer` output field: `big_idea`

---

## Sheet 4: Execution

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 25 | Labels |
| C-E | 30 | Content |
| F | 35 | Headlines |
| G | 10 | Ranking |
| H-L | 25 | Marketing argument columns |

### Section 9: Headlines & Lead (Rows 2-24)
- **B2**: Section header "9. Headlines & Lead" (merged B2:G2, yellow bg, 14pt bold)
- **B4**: "Headline sells the readership of the next part of the funnel, not the product"
- **B6**: "What is the one dominant resonant emotion we're trying to trigger?"
- **C6**: value
- **B8**: "The Lead:" | **C8**: lead copy
- **F10**: "Headlines / Subheadline Variants" (bold) | **G10**: "Ranking"
- **F11:G24**: Up to 14 headline variants with ranking scores
  - Content from `funnel-strategist` output field: `headlines`

### Section 10: Full Marketing Argument (Rows 26-52)
- **B26**: Section header "10. Full Marketing Argument" (merged B26:L26, yellow bg, 14pt bold)
- **G28**: "Sub Beliefs List" | **H28**: "Rank" | **I28**: "Claim" | **J28**: "Proof 1" | **K28**: "Proof 2" | **L28**: "*3 Matching Features & Benefits"
- **G29:L38**: Up to 10 sub-belief rows, each with rank, claim, 2 proofs, and 3 matching features/benefits
- **G40**: "Reminder of Thesis:" | **H40**: thesis recap
  - Content from `funnel-strategist` output field: `marketing_argument`

### Section 11: Minimum Viable Funnel (Rows 54-66)
- **B54**: Section header "11. Minimum Viable Funnel" (merged B54:G54, yellow bg, 14pt bold)
- **B56:G66**: MVF design
  - Content from `funnel-strategist` output field: `mvf`
  - Include: traffic source, lead magnet, landing page structure, email sequence outline, conversion path

### Section 12: MVF Response Flowchart (Rows 68-80)
- **B68**: Section header "12. MVF Response Flowchart" (merged B68:G68, yellow bg, 14pt bold)
- **B70:G80**: Flowchart description
  - Content from `funnel-strategist` output field: `response_flowchart`
  - Include: trigger events, response sequences, decision points, follow-up paths

### Section 13: Enhance (Rows 82-94)
- **B82**: Section header "13. Enhance" (merged B82:G82, yellow bg, 14pt bold, cyan bg #00FFFF)
- **B84:G94**: Enhancement strategies
  - Content from `funnel-strategist` output field: `enhance`
  - Include: A/B test priorities, conversion optimization targets, content upgrades, audience expansion

### Section 14: Expand (Rows 96-108)
- **B96**: Section header "14. Expand" (merged B96:G96, yellow bg, 14pt bold, cyan bg #00FFFF)
- **B98:G108**: Expansion strategies
  - Content from `funnel-strategist` output field: `expand`
  - Include: new channels, partnership opportunities, scaling strategy, market expansion, product line extensions

---

## Formatting Reference

### Section Header Style
```python
Font(name='Arial', size=14, bold=True)
PatternFill('solid', fgColor='FFFF00')  # Yellow
Alignment(horizontal='left', vertical='center')
```

### Sections 13-14 Header Style (different)
```python
Font(name='Arial', size=16, bold=True)
PatternFill('solid', fgColor='00FFFF')  # Cyan
```

### Column Header Style (competitor table, etc.)
```python
Font(name='Arial', size=13, bold=True)
Alignment(horizontal='center', vertical='center', wrap_text=True)
```

### Body Content Style
```python
Font(name='Arial', size=11)
Alignment(vertical='top', wrap_text=True)
```

### Sub-section Label Style
```python
Font(name='Arial', size=11, bold=True)
```
