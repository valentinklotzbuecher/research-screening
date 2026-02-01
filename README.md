# Research Paper Screening Tool

LLM-assisted paper screening for [The Unjournal](https://unjournal.org). Fetches papers from NEP-RepEc RSS feeds and screens them using a two-phase process to minimize API costs.

## How It Works

1. **Phase 1 (Fast/Cheap):** Screen title + abstract only
   - Papers scoring above threshold proceed to Phase 2
2. **Phase 2 (Full Analysis):** Download PDF and perform detailed screening
   - Papers scoring above threshold are flagged for human review

## Quick Start

1. Install dependencies:
   ```bash
   pip install feedparser requests anthropic  # or openai/google-generativeai
   ```

2. Set up API keys in `.Renviron`:
   ```
   ANTHROPIC_API_KEY=your-key-here
   ```

3. Configure parameters in `paper_screener.qmd` YAML front matter:
   ```yaml
   params:
     nep_category: "ain"      # NEP category code
     phase1_threshold: 50     # Min score for Phase 2
     phase2_threshold: 60     # Min score for recommendation
     provider: "anthropic"    # openai, anthropic, or google
   ```

4. Render:
   ```bash
   quarto render paper_screener.qmd
   ```

## NEP Categories

Find category codes at [nep.repec.org](https://nep.repec.org/). Examples:
- `ain` - Artificial Intelligence
- `dev` - Development
- `exp` - Experimental Economics
- `hea` - Health Economics
- `ure` - Urban and Real Estate Economics

## Output

- HTML report with Phase 1 and Phase 2 results
- JSON file saved to `screening_results/` with full details

## Screening Criteria

Based on Unjournal prioritization guidelines:
1. **Global decision-relevance** - Informs high-stakes welfare decisions
2. **Methodological interest** - Rigorous methods warranting expert review
3. **Evaluation value-add** - Public evaluation would provide useful signal
4. **Open science alignment** - Transparent, reproducible approaches
