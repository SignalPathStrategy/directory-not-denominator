# A strong directory, a weak denominator

**Can an industry's published top-100 ranking support the decisions people make with it?**
Eighteen years (2009–2026) of a voluntary, self-reported annual ranking of the largest firms in the custom integration channel, compiled into a documented dataset and tested for coverage, composition and selection.

Portfolio home: **https://signalpathstrategy.github.io/directory-not-denominator/**
Author: Alex Jaremko, SignalPath Consulting Group.
Built for the Google Data Analytics Professional Certificate capstone (Course 8, Track B — own data).

> **Status: in progress.** The data, code and cleaning log are final. The case-study narrative is being written. The knitted case study will be published here once complete.

## What is in this repository

| Path | What it is |
|---|---|
| `case_study.Rmd` | The R Markdown case study. Every chart and table regenerates from `data/`; nothing reaches back into private files. |
| `02_cleaning_and_decision_log.md` | The Process-phase log: sources, extraction, normalization, identity resolution, rulings, defects preserved as printed, limitations. |
| `data/cepro100_2009_2026_rows.csv` | The public extract: 1,807 firm-year rows, 379 firms, company names replaced by a stable `firm_id`. |
| `data/A1…A7_*.csv` | Aggregates from the list alone: yearly stats, same-store vs list-level growth, persistent cohort, entry/exit, size bands, years on list, status of every firm ever listed. |
| `data/B1…B3_*.csv` | Aggregates from an independent-roster reconstruction of the channel: landscape by population, firms above size thresholds, listed-vs-unlisted size distributions. Totals and shares only. |
| `data/key_stats.json` | The headline numbers in one place. |
| `index.html`, `resume.html`, `assets/` | The portfolio home served by GitHub Pages. |

## The three tests

1. **Composition** (`A2`, `A3`, `A4`) — Does year-over-year change in the list measure growth? Same-store median growth and list-level median change diverge; the 2026 edition is the extreme case: +8.0% on matched firms while the list-level median fell 32%, because 43 first-time entrants joined and retention fell to 58%.
2. **Coverage** (`B1`, `B2`) — How much of the population does the list capture? The printed list holds 98 of 411 identifiable firms and roughly one revenue dollar in every three and a half, and about a quarter of the firms at every size threshold.
3. **Selection** (`B3`, `A1`) — Are listed firms different from unlisted ones? The size distributions overlap heavily; participation, not size, determines membership.

Synthesis: the list is a strong **directory** and a weak **denominator**. Use it to find firms; do not use it to size a market, compute a growth rate, or benchmark a dealer.

## Reproduce

R 4.x with `tidyverse`, `scales`, `knitr` and `rmarkdown`. Open `case_study.Rmd` in RStudio and knit to HTML, or from a shell:

```
Rscript -e 'rmarkdown::render("case_study.Rmd")'
```

## Boundaries

- **No company names.** The printed data is public, and anyone can go to the source; this analysis does not call anyone out. The one exception is the five excluded outliers named in the cleaning log, because the exclusion is unintelligible without them and none is a custom integration firm.
- **Framing is limits, not errors.** The ranking is treated as an honest and competently produced artifact. Every limitation identified is structural — a property of voluntary, self-reported participation — not an error by its publisher.
- **Revenue estimates for unlisted firms** were produced by a documented desk method with a confidence rating on each; only totals, ranges and distributions are published. Firm-level estimates and the roster match table stay private.
- Generative AI was used for the extraction pipeline, desk research and drafting support, and is disclosed in the case study per the course's responsible-use guidance.

Source ranking: *CE Pro 100*, published annually by CE Pro (Emerald). The name is used to identify the source; this project is not affiliated with or endorsed by the publisher.
