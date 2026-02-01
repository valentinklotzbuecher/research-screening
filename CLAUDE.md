# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LLM-assisted paper screening tool for The Unjournal. A single Quarto document (`paper_screener.qmd`) that fetches papers from NEP-RepEc RSS feeds and screens them using a two-phase process:

1. **Phase 1**: Screen title + abstract (fast, cheap)
2. **Phase 2**: Download PDF and full screening (only for papers passing Phase 1)

## Rendering the Document

```bash
# Render to HTML (default)
quarto render paper_screener.qmd

# Render to PDF
quarto render paper_screener.qmd --to pdf
```

## Configuration

All screening parameters are set in the YAML front matter of `paper_screener.qmd`:

- `nep_category`: NEP category code (e.g., `"ain"`, `"dev"`, `"exp"`, `"hea"`) - see https://nep.repec.org/
- `phase1_threshold`: Minimum score (0-100) on title+abstract to proceed to PDF download
- `phase2_threshold`: Minimum score (0-100) on full PDF for final recommendation
- `provider`: `"openai"`, `"anthropic"`, or `"google"`
- `model`: Specific model ID or empty for provider default
- `focus_areas`: Comma-separated research priority areas

## Dependencies

Python packages (install via pip):
- `feedparser` - RSS feed parsing
- `requests` - HTTP requests for PDF download
- `anthropic`, `openai`, or `google-generativeai` - depending on provider

## API Keys

Keys are loaded from `.Renviron` (project or home directory):
- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`
- `GOOGLE_API_KEY` or `GEMINI_API_KEY`

## Architecture

The document is a literate programming file mixing R and Python:

1. **R setup block**: Validates parameters
2. **Python blocks**:
   - RSS feed fetching (`fetch_nep_feed`)
   - PDF discovery and download (`find_pdf_url`, `download_paper_pdf`)
   - Phase 1 screening functions (`screen_abstract_*`)
   - Phase 2 screening functions (`screen_pdf_*`)
   - Token pricing and cost calculation
   - JSON parsing from LLM responses
3. **R output blocks**: Render results tables and summaries for both phases

Data flows: RSS feed → Phase 1 (Python) → Filter → PDF download → Phase 2 (Python) → R (final rendering).

## Output

Results are saved to `screening_results/nep_{category}_YYYYMMDD_HHMMSS.json` with:
- `phase1_results`: All papers with abstract screening scores
- `phase2_results`: Papers that got full PDF screening
- Cost breakdown by phase
