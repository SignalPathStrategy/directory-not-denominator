# Cleaning and Decision Log

**Dataset:** eighteen annual editions of a trade publication's ranking of the 100 largest custom-integration firms, 2009–2026 list years (fiscal 2008–2025).
**Public extract:** `data/cepro100_2009_2026_rows.csv` — 1,807 firm-year rows, 379 unique firms, company names replaced by a stable firm identifier.
**Log kept by:** SignalPath Consulting Group, September 2026. Every step below is reproducible from the source PDFs with the pipeline in the private working folder; the public extract is the output of that pipeline with one column (the company name) removed.

---

## 1. Sources

| Editions | Format | Extraction method |
|---|---|---|
| 2020–2026 (seven) | Text-layer PDFs of the annual special report | Parsed programmatically by page geometry (`parse3.py`), validated row by row for numeric shape |
| 2009–2019 (eleven) | Scanned and text-layer issue PDFs, list spread across alternating pages with advertising between spreads | Parsed programmatically (`parse_old.py`), with one image-only page (2025 edition, page 30) transcribed by hand and flagged |

Each edition reports the **prior calendar year's** figures. The extract carries both `list_year` and `fiscal_year` so the two are never confused.

## 2. Columns that exist only in some years

The publisher changed its column set repeatedly. The extract keeps every column that was printed in at least one year; blanks mean *not published that year*, not *zero*.

| Field | Published in list years |
|---|---|
| Rank, company, city and state, revenue | Every year |
| Residential installs, employees | Every year |
| Commercial installs | 2020–2026 only |
| Percent recurring monthly revenue | 2019–2026 |
| Business outlook (free text) | 2012–2013, 2015–2019, 2023–2026 |
| Years in business | 2009–2014, 2020–2026 |
| Publisher's "verified" mark | 2009–2019 |
| Over-the-counter and online sales share | 2021–2022 only (not carried into the extract) |

## 3. Normalization applied

1. **Revenue and counts** converted from printed strings ("$12,345,678", "1,200") to integers. Anything that was not a clean number in print was retained verbatim in a notes field in the private workbook and left blank in the extract.
2. **State** derived from the printed city/state string, normalizing the publisher's changing abbreviation styles (two-letter codes from 2025; AP-style abbreviations earlier).
3. **Revenue per employee** recomputed as revenue ÷ employees for every row, alongside the publisher's own printed figure where one existed (2009–2020). The two occasionally disagree; the recomputed figure is used.
4. **Business outlook** free text ("Up 10%", "Flat", "Down 5%") parsed into a direction and, where given, a percentage.
5. **Revenue band** assigned to every row on a fixed scale (under $1M, $1–2M, $2–3M, $3–5M, $5–10M, $10–25M, $25M+).

## 4. Identity resolution across years

Firms rename, merge, get acquired and reappear under new names. Cross-year matching used:

- A **normalized name key**: case, punctuation and corporate suffixes (Inc, LLC, Group) removed; "doing business as" tails dropped.
- About **sixty manual aliases** for renames and print variants, each recorded with its evidence.
- **Eighteen identity merges** applied on 1 September 2026 after researching every firm that had left the list, collapsing 397 apparent firms to **379 unique firms**.
- **Deliberate non-merges** where similarly named firms are different businesses, separated by state (five such collisions) or by evidence (six similarly named pairs kept distinct).

Every merge and every non-merge is recorded in the private identity table. The public extract exposes only the resulting stable `firm_id`, assigned in order of first appearance.

## 5. Rulings that shape the numbers

These are analytical decisions, not extraction steps. Each was made deliberately and is flagged in the extract rather than silently applied.

| Ruling | What it does | Why |
|---|---|---|
| **Five-outlier exclusion** | Five entities that appear on the list but are not custom-integration businesses are flagged `outlier_excluded_from_excl_metrics = Y`: **ADT** (F0131) and **Vivint** (F0135), national security and smart-home service providers; **Guardian Protection** (F0001), a regional security company; **CCS Presentation Systems** (F0269), a commercial-AV integrator; and **Best Buy** (F0153), a consumer-electronics retailer's custom-installation line. They are the only firms named anywhere in this package; the exclusion is unintelligible without them, and none is a custom integration firm being singled out. They are kept in the extract and removed from every "excluding outliers" aggregate. | Without this, every total and concentration metric is dominated by businesses that do not operate like an integrator. The largest reported roughly 900 times the median listed firm's revenue at its peak. |
| **2025 correction applied** | The publisher issued a correction after printing the 2025 list: four firms were accidentally omitted and are inserted at their revenue positions; the bottom four printed rows they displace are removed; the list is re-ranked. Printed ranks are preserved in a separate column in the private workbook. | The corrected list is the publisher's own stated intent. It governs which firms count as alumni. |
| **Misprints kept as printed** | Four known geographic misprints (a city assigned to the wrong state) are kept verbatim in the city field and corrected only in the derived state field. One edition duplicates a row's install and employee counts; one printed revenue-per-employee figure is arithmetically impossible. All retained and flagged in the private notes. | The extract is a faithful record of what was published. Corrections are derived fields, never overwrites. |
| **Ties and rank gaps reproduced** | The publisher skips the next rank after a tie (33, 33, 35). Four editions print 101 rows and one prints 102 because of ties and late additions. One edition prints the same rank twice for different firms. All reproduced as printed; `tie` flags them. | Same principle. Rank is the publisher's ordering, not ours. |
| **Non-monotonic ordering left alone** | The publisher's ranking is occasionally not in revenue order (a lower rank with higher revenue). Reproduced as printed. | Same principle. |

## 6. Known defects in the source, preserved rather than fixed

- The publisher's own summary boxes and sidebar rankings do not always agree with its printed table: a 2024 total quoted at $6.33B where the table sums to $1.80B (the difference is one security company's full corporate revenue); a commercial-install total off by about 100; medians running 2–3% above the printed table's median; sidebar figures in one year repeating the prior year's values for several firms. These are recorded in the private workbook's benchmark reconciliation and are one of the reasons list-level figures are not used for trend claims.
- The revenue-per-customer sidebar changed its denominator between years without saying so.
- The 2011 edition prints ranks 26, 26 and 27 with three distinct revenues.

## 7. What the extract does not contain

- **Company names.** Replaced by `firm_id`. The source is a published list; the names are public and anyone can look them up. This case study does not name firms.
- **City.** Dropped; `state` is retained.
- The publisher's contact names (2009), buying-group affiliation (2012) and total-revenue-including-non-integration (2013–2014) columns — sparse, single-year fields with no analytical use here.
- **SignalPath's own revenue estimates** for firms that have never appeared on the list. Those are not published data and are not part of the public extract; the aggregate findings that use them (the coverage test) are published as totals and distributions only.

## 8. Limitations that travel with every finding

- Every figure is **self-reported by the firm and unaudited**. The publisher's verification mark, where it existed, was optional.
- Participation is **voluntary and changes every year** — on average about a third of the list turns over on a multi-year view. List-level totals and medians therefore move with who chose to participate, not only with the market. **Every trend claim in the analysis uses matched firms (same-store) or the persistent cohort, never list-level figures.**
- The list captures a **minority of the firms and revenue** in the population it is assumed to represent; quantifying that is the point of the coverage test, and the comparator rosters used for it have selection effects of their own (they are certification and buying-group memberships, not a census).
- Eighteen years spans the financial crisis, the pandemic-era boom and the current normalization. Same-store growth in any single year reflects that year's conditions, not a structural rate.
