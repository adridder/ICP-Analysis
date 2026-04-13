# ICP Analysis System

A self-contained [Claude Code](https://docs.anthropic.com/en/docs/claude-code) project that performs comprehensive ICP (Ideal Customer Profile) and competitor analysis for any company. Just provide a domain — it handles the rest and produces a professional xlsx spreadsheet.

## Quick Start

```bash
git clone https://github.com/alexderedder/ICP-Analysis.git
cd ICP-Analysis
claude
```

Then:

```
/icp-analysis notion.so
```

That's it. The system scrapes the company name from the domain, launches 4 parallel research agents, and produces a comprehensive analysis spreadsheet.

## Usage

### Auto mode (default) — just a domain

```
/icp-analysis figma.com
```

Full autonomous web research. No user input needed.

### Hybrid mode — your notes + web research

```
/icp-analysis acme.com --mode hybrid --data "Our main competitors are FooCorp and BarInc. We sell to enterprise DevOps teams. Price: $99/mo."
```

Provide what you know, the system researches the rest.

### Manual mode — your data only

```
/icp-analysis mycompany.com --mode manual --data "B2B SaaS for HR teams. We reduce employee turnover by 40%. Competitors: BambooHR, Gusto, Rippling. $12/user/mo."
```

Uses only your data. No web research.

## What It Produces

A professional xlsx spreadsheet (`output/{CompanyName}_ICP_Analysis_{date}.xlsx`) with 4 sheets and 14 analysis sections:

| # | Section | What It Contains |
|---|---------|-----------------|
| 1 | Prospect Analysis | Demographics, ideal client profile, Wants/Emotions/Beliefs, emotional triggers, before/after contrasting |
| 2 | Competitor Analysis | 6 competitors analyzed across 13 dimensions (hook, promise, USP, pricing, proof, guarantees, etc.) |
| 3 | Product Analysis | Product positioning, feature-benefit chains (functional, dimensionalized, emotional) |
| 4 | Promise Spectrum | 5 levels of messaging sophistication |
| 5 | Awareness Pyramid | Messaging strategy for each of Schwartz's 5 awareness levels |
| 6 | Irresistible Offer | Offer construction from highest-ranked features and deepest benefits |
| 7 | Marketing Thesis | The one thing the prospect must believe before buying |
| 8 | The Big Idea | Pattern-interrupt hook using the EC(PP+UM)+II=BI formula |
| 9 | Headlines & Lead | 8-14 headline variants with ranking, plus lead copy |
| 10 | Marketing Argument | Sub-beliefs, claims, proofs, and matching features |
| 11 | Minimum Viable Funnel | Traffic source, lead magnet, landing page, email sequence |
| 12 | Response Flowchart | Trigger events, decision points, follow-up paths |
| 13 | Enhance | A/B test priorities, conversion targets, content upgrades |
| 14 | Expand | New channels, partnerships, scaling, market expansion |

## How It Works

1. `/icp-analysis` resolves the company name from the domain
2. Four specialized research agents launch **in parallel**:
   - **Prospect Researcher** — mines reviews, forums, social media for prospect psychology
   - **Competitor Researcher** — finds 6 competitors (results-perspective) and scrapes their marketing
   - **Product Analyzer** — develops positioning, promise spectrum, awareness pyramid, thesis, Big Idea
   - **Funnel Strategist** — develops headlines, marketing argument, and funnel design
3. Each agent writes structured JSON to `output/research/`
4. The orchestrator synthesizes findings and ensures cross-section consistency
5. A professional xlsx spreadsheet is generated

## Project Structure

```
ICP-Analysis/
  CLAUDE.md                        # Claude Code project instructions
  README.md                        # This file
  LICENSE                          # Apache 2.0
  .claude/skills/icp-analysis/
    SKILL.md                       # Orchestrator skill
  references/
    xlsx-spec.md                   # Spreadsheet layout specification
    section-schemas.md             # JSON output schemas for all agents
    prospect-guide.md              # Prospect research methodology
    competitor-guide.md            # Competitor analysis methodology
    product-guide.md               # Product positioning framework
    funnel-guide.md                # Funnel strategy framework
  output/                          # Generated reports + intermediate JSON
  input/                           # Optional user data
```

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- Web search enabled in Claude Code (for auto/hybrid modes)

## Customization

- **Analysis framework**: Edit files in `references/` to change research methodology or add sections
- **Spreadsheet layout**: Edit `references/xlsx-spec.md` to modify sheets, columns, or formatting
- **Research sources**: Edit the guide files to add research sources or methodologies

## License

Apache 2.0 — see [LICENSE](LICENSE)

## Author

[Alexander De Ridder](https://github.com/alexderedder)
