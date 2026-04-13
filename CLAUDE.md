# ICP Analysis System

This project is a self-contained ICP (Ideal Customer Profile) and competitor analysis system built on Claude Code. It researches any company and produces a professional spreadsheet with full prospect analysis, competitor analysis, product positioning, marketing strategy, and funnel design.

## Quick Start

```
/icp-analysis notion.so
```

Just provide a domain. The system scrapes the company name automatically and runs full analysis.

## Modes

All modes are specified inline — no follow-up prompts needed:

```
/icp-analysis notion.so                           # auto mode (default)
/icp-analysis notion.so --mode hybrid --data "..." # hybrid: your notes + web research
/icp-analysis notion.so --mode manual --data "..." # manual: your data only, no web research
```

## How It Works

The `/icp-analysis` skill orchestrates 4 parallel research agents:
1. **Prospect Researcher** - Demographics, ideal client, W.E.B. analysis, emotional triggers
2. **Competitor Researcher** - 6 competitors with full marketing breakdown
3. **Product Analyzer** - Product positioning, promise spectrum, awareness pyramid, thesis, Big Idea
4. **Funnel Strategist** - Headlines, marketing argument, MVF, growth strategies

Each agent writes structured JSON to `output/research/`. The orchestrator synthesizes and generates a professional xlsx spreadsheet in `output/`.

## Project Structure

- `references/` - Research guides, JSON schemas, and xlsx layout specification
- `output/` - Generated analysis spreadsheets and intermediate research JSON
- `input/` - Drop files here for the system to incorporate
- `.claude/skills/icp-analysis/` - The orchestrator skill

## Requirements

- Claude Code with web search enabled (for auto/hybrid modes)

## Output

Final deliverable: `output/{CompanyName}_ICP_Analysis_{date}.xlsx` — a 4-sheet spreadsheet covering all 14 analysis sections.
