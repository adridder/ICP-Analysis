# ICP Analysis - Spreadsheet Specification

This document defines the exact structure, formatting, and content layout for the generated ICP Analysis xlsx report. The orchestrator reads this spec when generating the final spreadsheet via the `xlsx` skill.

---

## CRITICAL: Auto-Fit Row Heights

**Every row that contains content MUST have its height set to accommodate the text.** Use this formula after writing all cell values:

```python
import textwrap

def auto_height(ws):
    """Set row heights so all text is visible. Call AFTER all data is written."""
    for row in ws.iter_rows():
        max_lines = 1
        for cell in row:
            if cell.value and isinstance(cell.value, str):
                col_w = ws.column_dimensions.get(cell.column_letter)
                width = col_w.width if col_w and col_w.width else 10
                chars_per_line = max(int(width * 1.2), 1)
                # Count explicit newlines AND wrapped lines
                for paragraph in str(cell.value).split('\n'):
                    max_lines += max(1, -(-len(paragraph) // chars_per_line))  # ceil division
                max_lines += str(cell.value).count('\n')
            elif cell.value:
                max_lines = max(max_lines, 1)
        row_height = max(20, min(max_lines * 15, 409))  # Excel max row height is 409
        ws.row_dimensions[row[0].row].height = row_height
```

**Call `auto_height(ws)` on every sheet after writing all data.** This is the single most important formatting step.

For rows where the calculated height exceeds 409 (Excel's max), the text will still be cut off. In that case, **split the content across multiple rows** rather than cramming it into one cell.

---

## Global Formatting

### Color Palette
| Name | Hex | Usage |
|------|-----|-------|
| Header Yellow | `#FFF2CC` | Section header backgrounds (warm, not harsh) |
| Header Text | `#1A1A1A` | Dark gray, easier on eyes than pure black |
| Table Header | `#4472C4` | Blue background for table column headers |
| Table Header Text | `#FFFFFF` | White text on blue headers |
| Alt Row | `#F2F7FB` | Light blue-gray for alternating rows |
| White | `#FFFFFF` | Default row background |
| Label BG | `#E8E8E8` | Light gray for label/key cells (column A/B) |
| Border | `#D0D0D0` | Light gray borders |
| Accent Cyan | `#E0F7FA` | Sections 13-14 header background |
| Threat Direct | `#FFFFFF` | No fill |
| Threat Adjacent | `#FFE0E0` | Light red |
| Threat BuildBuy | `#FFF0D0` | Light orange |
| Threat Emerging | `#E0F0FF` | Light blue |

### Font
- **Primary font**: Aptos (modern, clean). Fall back to Calibri or Arial if unavailable.
- **Section headers**: 16pt bold, color `#1A1A1A`
- **Table column headers**: 11pt bold, color `#FFFFFF`
- **Sub-section labels**: 11pt bold, color `#1A1A1A`
- **Body text**: 11pt regular, color `#333333`
- **Small notes/guidance**: 10pt italic, color `#666666`

### Borders
- **Table cells**: Thin borders on all sides, color `#D0D0D0`
- **Section headers**: Thick bottom border, color `#4472C4`
- **Between sections**: Leave 1 empty row as visual spacer

### Alignment
- **All content cells**: `vertical='top'`, `wrap_text=True`
- **Table headers**: `horizontal='center'`, `vertical='center'`, `wrap_text=True`
- **Number/rank cells**: `horizontal='center'`
- **Label cells (column A/B)**: `vertical='top'`

### Freeze Panes
- Sheet 1: Freeze row 2 (header always visible)
- Sheet 2: Freeze row 6 (column headers always visible when scrolling competitor data)
- Sheet 3: Freeze row 2
- Sheet 4: Freeze row 2

---

## Sheet Structure

The workbook contains **4 sheets**:

### Sheet 1: "Prospect Analysis" (Section 1)
### Sheet 2: "Competitor & Product" (Sections 2-3)
### Sheet 3: "Strategy" (Sections 4-8)
### Sheet 4: "Execution" (Sections 9-14)

Tab colors: Sheet 1 = `#4472C4` (blue), Sheet 2 = `#ED7D31` (orange), Sheet 3 = `#70AD47` (green), Sheet 4 = `#7030A0` (purple)

### Branding Row (EVERY sheet)

The **first row** of every sheet is the branding row. All section content shifts down by 2 rows to accommodate it.

- **A1**: "ICP Analysis by Alexander De Ridder" (Aptos, 10pt, bold, color `#4472C4`)
- **D1**: "linkedin.com/in/adridder" (Aptos, 10pt, color `#4472C4`, underlined) — this is a hyperlink to `https://linkedin.com/in/adridder`
- Row 1 height: 20
- Row 1 has no borders, no fill — clean and minimal

```python
from openpyxl.styles import Font

branding_font = Font(name='Aptos', size=10, bold=True, color='4472C4')
link_font = Font(name='Aptos', size=10, color='4472C4', underline='single')

# Apply to every sheet:
ws['A1'] = 'ICP Analysis by Alexander De Ridder'
ws['A1'].font = branding_font
ws['D1'] = 'linkedin.com/in/adridder'
ws['D1'].font = link_font
ws['D1'].hyperlink = 'https://linkedin.com/in/adridder'
ws.row_dimensions[1].height = 20
```

**Important**: Because row 1 is now the branding row, all section content described below starts at the row numbers specified (which already account for this). The branding row is ABOVE the section headers.

---

## Sheet 1: Prospect Analysis

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 5 | Row labels (A-E) |
| B | 28 | Sub-section titles |
| C | 40 | Primary content |
| D | 35 | Content continued |
| E | 35 | Content continued |
| F | 35 | Content continued |
| G | 35 | Content continued |

### Section 1A: Demographics (Rows 2-16)
- **Row 2**: Section header "1. Prospect Analysis" (merged B2:G2, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 3**: Empty spacer row
- **A4**: "A" (bold, `#E8E8E8` bg)
- **B4**: "Demographics" (bold, `#E8E8E8` bg)
- **C4:G4**: Merged — demographic summary paragraph
- **Rows 5-16**: Individual demographic fields, one per row:
  - B5: "Age Range" | C5: value
  - B6: "Gender Split" | C6: value
  - B7: "Income Level" | C7: value
  - B8: "Education" | C8: value
  - B9: "Geography" | C9: value
  - B10: "Industry" | C10: value
  - B11: "Job Titles" | C11: value (comma-separated list)
  - B12: "Company Size" | C12: value
  - B13: "Psychographics" | C13:G13: merged, value
  - Label cells (B column): bold, `#E8E8E8` bg, thin borders
  - Value cells: thin borders, wrap text
  - Content from `prospect-researcher` output field: `demographics`

### Section 1B: Ideal Client Targeting (Rows 18-24)
- **Row 17**: Empty spacer
- **A18**: "B" (bold, `#E8E8E8` bg)
- **B18**: "Ideal Client Targeting" (bold, `#E8E8E8` bg)
- **C18:G18**: Merged — prompt text (10pt italic, `#666666`)
- **B19**: "Success Story" | **C19:G19**: merged, value
- **B20**: "Results Achieved" | **C20:G20**: merged, value (bullet list)
- **B21**: "Indirect Transformations" | **C21:G21**: merged, value (bullet list)
- **B22**: "Profile Summary" | **C22:G24**: merged, value (paragraph)
  - Content from `prospect-researcher` output field: `ideal_client`

### Section 1C: W.E.B. Analysis (Rows 26-40)
- **Row 25**: Empty spacer
- **A26**: "C" (bold, `#E8E8E8` bg)
- **B26**: "W.E.B. Analysis" (bold, `#E8E8E8` bg)
- **C26:G26**: Merged — "Wants, Emotions, Beliefs" (10pt italic)
- Break out into individual rows:
  - B27: "Wants to Gain" | C27:G27: merged, bullet list
  - B28: "Wants to Be" | C28:G28: merged, bullet list
  - B29: "Wants to Do" | C29:G29: merged, bullet list
  - B30: "Wants to Save" | C30:G30: merged, bullet list
  - B31: "Wants to Avoid" | C31:G31: merged, bullet list
  - B33: "Key Emotions" | C33:G33: merged, comma-separated
  - B34: "Core Beliefs" | C34:G34: merged, bullet list
  - Content from `prospect-researcher` output field: `web_analysis`
  - Row 32: empty spacer between wants and emotions

### Section 1D: Emotional Triggers (Rows 36-41)
- **Row 35**: Empty spacer
- **A36**: "D" (bold, `#E8E8E8` bg)
- **B36**: "Emotional Triggers" (bold, `#E8E8E8` bg)
- **C36**: "Feelings the prospect wants to buy or avoid" (10pt italic)
- **C37:F40**: 4x4 grid of emotional triggers
  - Each cell: centered, thin borders, 11pt
  - Alternating row colors (`#F2F7FB` / white)
  - Content from `prospect-researcher` output field: `emotional_triggers`

### Section 1E: Now/After Contrasting (Rows 43-51)
- **Row 42**: Empty spacer
- **A43**: "E" (bold, `#E8E8E8` bg)
- **B43**: "Now / After Contrasting" (bold, `#E8E8E8` bg)
- **C43:D43**: "NOW (Before)" header | **E43:F43**: "AFTER" header
  - Headers: bold, centered, `#4472C4` bg, white text
- Row layout (one pair per row):
  - B44: "Have" | C44: now value | E44: after value
  - B45: "Avg Experience" | C45: now value | E45: after value
  - B46: "Status" | C46: now value | E46: after value
  - B47: "Feel" | C47: now value | E47: after value
  - All cells: thin borders, wrap text
  - "NOW" column: light red bg `#FFF0F0`
  - "AFTER" column: light green bg `#F0FFF0`
  - Content from `prospect-researcher` output field: `now_after`

---

## Sheet 2: Competitor & Product

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 6 | Number |
| C | 16 | Threat Type |
| D | 22 | Name |
| E | 35 | Hook (Idea) |
| F | 40 | Primary Marketing Promise |
| G | 30 | Delivery Mechanism |
| H | 35 | USP |
| I | 40 | General Marketing Claims |
| J | 35 | Proof Points |
| K | 35 | Benefit Statements |
| L | 30 | Deliverables/Features |
| M | 20 | Price/Terms |
| N | 25 | Bonuses |
| O | 25 | Risk-Reversal |

### Section 2: Competitor Analysis (Rows 2-14)
- **Row 2**: Section header "2. Competitor Analysis" (merged B2:O2, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 3**: Empty spacer
- **B4:O4**: Instruction text: "TOP Competitors (Results-perspective) — across direct, adjacent platform, build-vs-buy, and emerging threats" (10pt italic, `#666666`)
- **Row 5**: Empty spacer
- **B6:O6**: Column headers (bold, `#4472C4` bg, white text, centered, thin borders):
  `# | Threat Type | Name | Hook | Primary Marketing Promise | Delivery Mechanism | USP | Marketing Claims | Proof Points | Benefits | Deliverables | Price/Terms | Bonuses | Risk-Reversal`
- **B7:O12**: 6 data rows (one per competitor)
  - All cells: thin borders on all sides (`#D0D0D0`), wrap text, vertical align top
  - Alternating row backgrounds: white / `#F2F7FB`
  - Threat Type column (C) color-coded:
    - Direct: no special fill
    - Adjacent Platform: `#FFE0E0`
    - Build vs. Buy: `#FFF0D0`
    - Emerging: `#E0F0FF`
  - Content from `competitor-researcher` output field: `competitors[]`
- **Row 13**: Empty spacer
- **B14:O14**: Threat Assessment (merged, `#E8E8E8` bg, thin borders)
  - Content from `competitor-researcher` output field: `threat_assessment`

### Section 3: Product Analysis (Rows 17-45)
- **Row 16**: Empty spacer
- **Row 17**: Section header "3. Our Product Analysis" (merged B17:O17, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 18**: Empty spacer
- Product attributes, one per row:
  - B19: "Primary Promise" | C19:G19: merged, value
  - B20: "Proof Points" | C20:G20: merged, value
  - B21: "Credibility Factors" | C21:G21: merged, value
  - B22: "USP" | C22:G22: merged, value
  - B23: "Unique Delivery Mechanism" | C23:G23: merged, value
  - B24: "Track Record" | C24:G24: merged, value
  - B25: "Performance Metrics" | C25:G25: merged, value
  - B26: "Interest Points" | C26:G26: merged, value
  - Label cells (B): bold, `#E8E8E8` bg, thin borders
  - Value cells: thin borders, wrap text
  - Content from `product-analyzer` output field: `product_analysis`

#### Features & Benefits Sub-table (Rows 29-45)
- **Row 28**: Empty spacer
- **B29:F29**: Column headers (bold, `#4472C4` bg, white text, thin borders):
  `Feature | Functional Benefit | Dimensionalized Benefit | Emotional Benefit | Rank`
- Column widths for this table: B=25, C=40, D=40, E=40, F=8
- **B30:F41**: Up to 12 feature rows
  - All cells: thin borders, wrap text
  - Alternating rows: white / `#F2F7FB`
  - Rank column (F): centered
  - Content from `product-analyzer` output field: `features[]`

---

## Sheet 3: Strategy

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 30 | Labels |
| C | 40 | Content |
| D | 40 | Content |
| E | 40 | Content |
| F | 40 | Content |
| G | 40 | Content |
| H | 40 | Content |
| I | 45 | Notes/guidance |

### Section 4: Promise Exposure Spectrum (Rows 2-12)
- **Row 2**: Section header "4. Promise Exposure Spectrum" (merged B2:H2, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 3**: Empty spacer
- **B4**: "Develop the Promise" (bold)
- **Row 5**: Empty spacer
- **B6:F6**: Level headers (bold, `#4472C4` bg, white text, centered, thin borders):
  `Level 1 (Basic) | Level 2 (Specific) | Level 3 (Mechanism) | Level 4 (Detailed) | Level 5 (Prospect-Led)`
- **B7:F7**: Promise statements — one cell per level
  - All cells: thin borders, wrap text
  - Content from `product-analyzer` output field: `promise_spectrum[]`
- **I6**: "As you move right, messaging sophistication increases" (10pt italic, `#666666`)

### Section 5: Prospect Awareness Pyramid (Rows 14-30)
- **Row 13**: Empty spacer
- **Row 14**: Section header "5. Prospect Awareness Pyramid" (merged B14:H14, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 15**: Empty spacer
- **B16**: "Talk to the prospect at their awareness level" (bold)
- **Row 17**: Empty spacer
- **B18:E18**: Column headers (bold, `#4472C4` bg, white text, thin borders):
  `Awareness Level | Description | Messaging Approach | Example Copy`
- **B19:E23**: 5 awareness level rows (one per level)
  - Levels: Most Aware, Product Aware, Solution Aware, Problem Aware, Unaware
  - All cells: thin borders, wrap text
  - Alternating rows: white / `#F2F7FB`
  - Content from `product-analyzer` output field: `awareness_pyramid`

### Section 6: Superior & Irresistible Offer (Rows 32-46)
- **Row 31**: Empty spacer
- **Row 32**: Section header "6. Superior & Irresistible Offer" (merged B32:H32, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 33**: Empty spacer
- Key-value rows:
  - B34: "Deliverables" | C34:G34: merged, value
  - B35: "Top Features" | C35:G35: merged, value (numbered list, top 5-7)
  - B37: "Why" | C37:G37: merged, value
  - B38: "Functional Benefits" | C38:G38: merged, value
  - B39: "Dimensionalized Benefits" | C39:G39: merged, value
  - B40: "Emotional Benefits" | C40:G40: merged, value
  - Row 36: empty spacer
  - Label cells: bold, `#E8E8E8` bg, thin borders
  - Value cells: thin borders, wrap text
  - Content from `product-analyzer` output field: `irresistible_offer`

### Section 7: Marketing Thesis (Rows 48-66)
- **Row 47**: Empty spacer
- **Row 48**: Section header "7. Marketing Thesis" (merged B48:H48, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 49**: Empty spacer
- Key-value rows:
  - B50: "Thesis Statement" | C50:G52: merged, value (the one belief)
  - B54: "Thesis Solution" | C54:G54: merged, value (unique mechanism)
  - B55: "Our Thesis" | C55:G55: merged, "To get X, you need..."
  - B56: "Product Connection" | C56:G56: merged, "To get X, you need our product because..."
  - Row 53: empty spacer
  - Content from `product-analyzer` output field: `marketing_thesis`
- **Row 58**: Empty spacer
- **B59:D59**: Marketing Argument Order headers (bold, `#4472C4` bg, white text):
  `1. Thesis Solution | 2. Our Marketing Thesis | 3. Product`
- **B60:D60**: Values
  - Content from `product-analyzer` output field: `marketing_thesis`

### Section 8: The Big Idea (Rows 68-90)
- **Row 67**: Empty spacer
- **Row 68**: Section header "8. The Big Idea" (merged B68:H68, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 69**: Empty spacer
- Key-value rows (with thin borders, label cells `#E8E8E8`):
  - B70: "1 Emotion" | C70: value
  - B71: "1 Promise" | C71: value
  - B72: "1 Story" | C72: value
  - B73: "1 Thing to Think" | C73: value
  - Row 74: empty spacer
  - B75: "Primary Promise (PP)" | C75:G75: merged, value
  - B76: "Unique Mechanism (UM)" | C76:G76: merged, value
  - Row 77: empty spacer
  - B78: "Emotionally Compelling (EC)" | C78:G78: merged, value
  - B79: "Intellectually Interesting (II)" | C79:G79: merged, value
  - B80: "Big Idea (BI)" | C80:G80: merged, value (bold, larger text 13pt)
  - Row 81: empty spacer
  - B82: "Formula" | C82: "EC (PP + UM) + II = BI" (bold, 13pt, `#4472C4` color)
- **I70:I73**: Guidance notes (10pt italic, `#666666`):
  - I70: "Overcome 'I've seen this before' to prevent mental opt-out"
  - I71: "The Big Idea is a pattern-interrupt"
  - I72: "The 'ad' for the funnel, NOT the offer"
  - I73: "Every headline is a different expression of the Big Idea"
- **Row 84**: "Idea Variations" (bold)
- **B85:G89**: List of 3-5 Big Idea variations
  - Content from `product-analyzer` output field: `big_idea`

---

## Sheet 4: Execution

### Column Widths
| Column | Width | Purpose |
|--------|-------|---------|
| A | 4 | Spacer |
| B | 30 | Labels |
| C | 40 | Content |
| D | 40 | Content |
| E | 40 | Content |
| F | 45 | Headlines |
| G | 12 | Ranking |
| H | 30 | Sub Beliefs |
| I | 10 | Rank |
| J | 35 | Claim |
| K | 35 | Proof 1 |
| L | 35 | Proof 2 |

### Section 9: Headlines & Lead (Rows 2-30)
- **Row 2**: Section header "9. Headlines & Lead" (merged B2:G2, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 3**: Empty spacer
- **B4**: "Headline sells the readership of the next part of the funnel, not the product" (10pt italic, `#666666`)
- **Row 5**: Empty spacer
- **B6**: "Dominant Emotion" (bold, `#E8E8E8` bg) | **C6:E6**: merged, value, thin borders
- **Row 7**: Empty spacer
- **B8**: "The Lead" (bold, `#E8E8E8` bg) | **C8:E10**: merged, lead copy, thin borders
- **Row 12**: Empty spacer
- **F13:G13**: Column headers (bold, `#4472C4` bg, white text, thin borders):
  `Headline / Subheadline | Ranking`
- **F14:G27**: Up to 14 headline variants with ranking scores
  - All cells: thin borders, wrap text
  - Alternating rows: white / `#F2F7FB`
  - Ranking column: centered
  - Content from `funnel-strategist` output field: `headlines`

### Section 10: Full Marketing Argument (Rows 32-52)
- **Row 31**: Empty spacer
- **Row 32**: Section header "10. Full Marketing Argument" (merged B32:L32, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 33**: Empty spacer
- **H34:L34**: Column headers (bold, `#4472C4` bg, white text, thin borders):
  `Sub Belief | Rank | Claim | Proof 1 | Proof 2`
- **H35:L44**: Up to 10 sub-belief rows
  - All cells: thin borders, wrap text
  - Alternating rows: white / `#F2F7FB`
  - Rank column: centered
  - Content from `funnel-strategist` output field: `marketing_argument`
- **Row 46**: Empty spacer
- **H47**: "Reminder of Thesis:" (bold, `#E8E8E8` bg) | **I47:L47**: merged, thesis recap, thin borders

### Section 11: Minimum Viable Funnel (Rows 54-72)
- **Row 53**: Empty spacer
- **Row 54**: Section header "11. Minimum Viable Funnel" (merged B54:G54, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 55**: Empty spacer
- Key-value rows (label cells bold, `#E8E8E8` bg, thin borders):
  - B56: "Traffic Source" | C56:E56: merged, value
  - B57: "Lead Magnet" | C57:E57: merged, value
  - B58: "Landing Page Structure" | C58:E60: merged, value
  - B61: "Email Sequence" | C61:E64: merged, value
  - B65: "Conversion Path" | C65:E65: merged, value
  - B66: "Primary CTA" | C66:E66: merged, value
  - Content from `funnel-strategist` output field: `mvf`

### Section 12: MVF Response Flowchart (Rows 74-88)
- **Row 73**: Empty spacer
- **Row 74**: Section header "12. MVF Response Flowchart" (merged B74:G74, `#FFF2CC` bg, 16pt bold, thick bottom border)
- **Row 75**: Empty spacer
- Key-value rows:
  - B76: "Trigger Events" | C76:E78: merged, bullet list
  - B79: "Response Sequences" | C79:E81: merged, bullet list
  - B82: "Decision Points" | C82:E84: merged, bullet list
  - B85: "Follow-up Paths" | C85:E87: merged, bullet list
  - Content from `funnel-strategist` output field: `response_flowchart`

### Section 13: Enhance (Rows 90-106)
- **Row 89**: Empty spacer
- **Row 90**: Section header "13. Enhance" (merged B90:G90, `#E0F7FA` bg, 16pt bold, thick bottom border)
- **Row 91**: Empty spacer
- Key-value rows:
  - B92: "A/B Test Priorities" | C92:E94: merged, numbered list
  - B95: "Conversion Targets" | C95:E97: merged, bullet list with metrics
  - B98: "Content Upgrades" | C98:E100: merged, bullet list
  - B101: "Audience Expansion" | C101:E103: merged, bullet list
  - Content from `funnel-strategist` output field: `enhance`

### Section 14: Expand (Rows 108-124)
- **Row 107**: Empty spacer
- **Row 108**: Section header "14. Expand" (merged B108:G108, `#E0F7FA` bg, 16pt bold, thick bottom border)
- **Row 109**: Empty spacer
- Key-value rows:
  - B110: "New Channels" | C110:E112: merged, bullet list
  - B113: "Partnerships" | C113:E115: merged, bullet list
  - B116: "Scaling Strategy" | C116:E118: merged, value
  - B119: "Market Expansion" | C119:E121: merged, value
  - B122: "Product Extensions" | C122:E124: merged, bullet list
  - Content from `funnel-strategist` output field: `expand`

---

## Formatting Reference (openpyxl code)

### Section Header Style
```python
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

section_header_font = Font(name='Aptos', size=16, bold=True, color='1A1A1A')
section_header_fill = PatternFill('solid', fgColor='FFF2CC')
section_header_alignment = Alignment(horizontal='left', vertical='center')
section_header_border = Border(bottom=Side(style='thick', color='4472C4'))
```

### Table Column Header Style
```python
table_header_font = Font(name='Aptos', size=11, bold=True, color='FFFFFF')
table_header_fill = PatternFill('solid', fgColor='4472C4')
table_header_alignment = Alignment(horizontal='center', vertical='center', wrap_text=True)
table_header_border = Border(
    top=Side(style='thin', color='D0D0D0'),
    bottom=Side(style='thin', color='D0D0D0'),
    left=Side(style='thin', color='D0D0D0'),
    right=Side(style='thin', color='D0D0D0')
)
```

### Label Cell Style (B column keys)
```python
label_font = Font(name='Aptos', size=11, bold=True, color='1A1A1A')
label_fill = PatternFill('solid', fgColor='E8E8E8')
label_alignment = Alignment(vertical='top', wrap_text=True)
label_border = Border(
    top=Side(style='thin', color='D0D0D0'),
    bottom=Side(style='thin', color='D0D0D0'),
    left=Side(style='thin', color='D0D0D0'),
    right=Side(style='thin', color='D0D0D0')
)
```

### Body Content Style
```python
body_font = Font(name='Aptos', size=11, color='333333')
body_alignment = Alignment(vertical='top', wrap_text=True)
body_border = Border(
    top=Side(style='thin', color='D0D0D0'),
    bottom=Side(style='thin', color='D0D0D0'),
    left=Side(style='thin', color='D0D0D0'),
    right=Side(style='thin', color='D0D0D0')
)
```

### Alternating Row Fill
```python
alt_row_fill = PatternFill('solid', fgColor='F2F7FB')
white_fill = PatternFill('solid', fgColor='FFFFFF')
# Apply: alt_row_fill if row_index % 2 == 0 else white_fill
```

### Guidance/Notes Style
```python
notes_font = Font(name='Aptos', size=10, italic=True, color='666666')
```

### Sections 13-14 Header (Cyan variant)
```python
cyan_header_fill = PatternFill('solid', fgColor='E0F7FA')
```
