# Optimal Funding Trajectories for Follow-On Investment
### Project 2: Exploratory Data Analysis (EDA) — Startup Investments
#### OHOOD MAJED AHMED

---

## Problem Statement

Using historical Crunchbase data on seed-funded startups, which factors — funding amount, industry, geography, and timeline — are most associated with a startup successfully raising a follow-on round (Series A+), and how can those factors inform a data-driven $10M seed-stage investment thesis?

---

## Executive Summary

This project analyzes a Crunchbase dataset of roughly 54,000 companies to understand what separates seed-stage startups that go on to raise a follow-on round (Series A or later) from those that stall after their first check. After removing ~4,856 structurally blank rows, correcting corrupted date fields (including impossible pre-1950 and pre-founding funding dates), converting mis-typed numeric columns, and excluding a handful of manually confirmed non-startup institutions, the cleaned dataset was filtered down to a seed-stage cohort — companies that raised seed and/or angel funding. A new binary target, `reached_followon`, was engineered to flag whether a company later raised any Series A–H round.

Exploratory analysis and statistical testing (Mann-Whitney U tests and Chi-square tests of independence) show that only about 13.4% of seed-stage startups ever reach a follow-on round. Seed check size is the strongest single signal found: companies that went on to raise a follow-on round had a median seed round of $742K, nearly 5x the $150K median of companies that stalled (p < 0.001). Industry category and geography are also both significantly associated with follow-on success (p < 0.001 for both): Enterprise Software, Fintech, Analytics, and Biotechnology outperform the market average by roughly 1.5–2x, and the USA and Israel substantially outperform other major startup hubs. Among startups that do progress, the typical gap between their seed round and most recent round is about 768 days (~2.1 years). Notably, the speed at which a startup raises its first round after founding shows no significant relationship with follow-on success (p = 0.237), a useful negative finding.

These patterns translate into a concrete set of recommendations for the firm's $10M seed-stage thesis: prioritize Enterprise Software, Fintech, Analytics, and Biotechnology startups; favor larger seed checks near or above the $742K historical median; set an 18–24 month post-seed monitoring checkpoint; and weight US- and Israel-based teams more favorably. Because the analysis is correlational rather than causal and the dataset's funding records run through roughly 2014, these criteria should be validated against more recent data before being finalized as investment policy.

---

## File Directory

| File / Folder | Description |
| :--- | :--- |
| `README.md` | This file — project overview, findings, and conclusions. |
| `Data/startup_investments_raw.csv` | Original, unmodified Crunchbase dataset as provided. |
| `Data/startup_investments_cleaned.csv` | Cleaned dataset produced at the end of the data-cleaning process. |
| `Code/03_EDA.ipynb` | Main analysis notebook: data collection, cleaning, EDA, statistical testing, and findings (this project does not include a separate modeling notebook — see note below). |
| `Presentation/startup_investments_presentation.pdf` | Slide deck summarizing the problem, process, and findings. |
| `Presentation/Images/` | Supporting chart images used in the presentation, if exported separately. |
| `Scratch/` | Exploratory work and drafts not intended for evaluation. |

**Note:** Per the assignment scope, this is an EDA-only project — a `04_Modeling` notebook is not applicable here and will be introduced in a future project.

---

## Data & Data Dictionary

**Source:** Crunchbase startup funding dataset (company profiles, funding rounds, and amounts), covering ~54,000 companies with funding records running through roughly 2014.

| Feature / Column | Data Type | Source | Description |
|:---|:---|:---|:---|
| `permalink` | string | Original | Unique identifier URL path for each company profile. |
| `name` | string | Original | The official operating name of the startup company. |
| `homepage_url` | string | Original | The primary web address/website of the company. |
| `category_list` | string | Original | Raw list of industry categories and tags associated with the startup. |
| `market` | string | Original (cleaned) | Primary industry/market segment of the company; whitespace-stripped. |
| `funding_total_usd` | float | Original (cleaned) | Total funding raised across all rounds, in USD. Originally stored as text with comma-grouped digits; converted to numeric. |
| `status` | string | Original | Current operational state of the company (e.g., operating, acquired, closed). |
| `country_code` | string | Original (cleaned) | 3-letter ISO country code of company headquarters. |
| `state_code` | string | Original | Administrative state/province code of company headquarters. |
| `region` | string | Original | Broad geographic or metropolitan region of the company. |
| `city` | string | Original | Specific city where the company headquarters is located. |
| `funding_rounds` | int | Original (cleaned) | Total number of distinct funding rounds raised; cast from float64 to nullable `Int64`. |
| `founded_month` / `founded_quarter` / `founded_year` | string / string / int | Original (cleaned) | Month, quarter, and year portions of the founding date; `founded_year` cast to nullable `Int64`. |
| `seed` | float | Original | Total amount raised via seed funding rounds, in USD. |
| `angel` | float | Original | Total amount raised from angel investors, in USD. |
| `venture` | float | Original | Total amount raised via venture capital rounds, in USD. |
| `equity_crowdfunding` | float | Original | Total capital raised through equity crowdfunding platforms, in USD. |
| `undisclosed` | float | Original | Total funding from rounds where details were kept private, in USD. |
| `convertible_note` | float | Original | Total amount raised through convertible debt instruments, in USD. |
| `debt_financing` | float | Original | Total amount raised via standard debt or loan instruments, in USD. |
| `grant` | float | Original | Total non-dilutive capital received from government or private grants, in USD. |
| `private_equity` | float | Original | Total capital raised from private equity firms, in USD. |
| `post_ipo_equity` / `post_ipo_debt` | float | Original | Equity/debt capital raised after the company went public, in USD. |
| `secondary_market` | float | Original | Total value of shares traded or sold via secondary market transactions, in USD. |
| `product_crowdfunding` | float | Original | Total non-equity funds raised via product platforms (e.g., Kickstarter), in USD. |
| `round_A` … `round_H` | float | Original | Total amount raised in each respective Series A–H funding round, in USD. |
| `founded_at` / `first_funding_at` / `last_funding_at` | datetime | Original (cleaned) | Parsed date fields; corrupted/implausible entries (pre-1950 dates, funding predating founding by >365 days) nulled per documented rules in the notebook. |
| `reached_followon` | int (0/1) | **Engineered** | 1 if the company recorded any funding in `round_A` through `round_H`, else 0. Defines "follow-on success." |
| `seed_cohort` | DataFrame subset | **Engineered** | Boolean-masked subset of companies with seed and/or angel funding > 0 — the analysis population. |
| `days_to_first_funding` | int | **Engineered** | Days between `founded_at` and `first_funding_at`; tests whether raise speed predicts follow-on success. |
| `days_first_to_last` | int | **Engineered** | Days between `first_funding_at` and `last_funding_at`; measures typical time between seed and latest round. |

---

## Conclusions & Recommendations

**Key Findings**
- Only **~13.4%** of seed-stage startups in this dataset ever raise a follow-on round — the baseline any new selection criteria needs to beat.
- **Seed check size** is the strongest signal found: successful companies had a median seed round of **$742K** vs. **$150K** for unsuccessful ones (Mann-Whitney U, p < 0.001).
- **Industry category** is significantly associated with follow-on success (Chi-square, p < 0.001): Enterprise Software, Fintech, Analytics, and Biotechnology outperform the market average by roughly 1.5–2x.
- **Geography matters**: the USA (18.8%) and Israel (20.4%) substantially outperform other major startup hubs (Chi-square, p < 0.001).
- **Time to next round**: among startups that progress, the typical gap between seed and their latest round is **~768 days (~2.1 years)**, with a 25th–75th percentile range of 430–1,297 days.
- **Speed to first funding does not matter**: no significant relationship was found between how quickly a startup raises its first round and eventual follow-on success (p = 0.237).

**Recommendations**
- Prioritize the firm's $10M seed-stage capital toward **Enterprise Software, Fintech, Analytics, and Biotechnology** startups.
- Favor **larger seed checks** (at or above the ~$742K historical median for successful companies) over small/token checks.
- Set an **18–24 month (or strict 768-day) monitoring checkpoint** post-seed; companies without follow-on momentum by then are trending toward the historical "stalled" pattern.
- Weight **US- and Israel-based teams** more favorably given their stronger follow-on ecosystems, without excluding other regions outright.
- Do **not** penalize startups for taking longer to close their first round — this showed no predictive value.

---

## Areas for Further Research

- This analysis is **correlational, not causal** — larger seed checks may cause follow-on success (more runway), or may simply reflect investor conviction that itself predicts quality. A causal study (e.g., matching or instrumental variables) would be needed to disentangle this.
- The dataset's funding records run through roughly 2014, so category dynamics (e.g., the more recent rise of AI/ML-focused startups) may have shifted materially since. Findings should be validated against a more recent funding dataset before being finalized as investment criteria.
- "Follow-on success" as defined here is binary and does not account for round size or valuation — a company could raise a small follow-on round and still underperform financially.
- Future work (a `04_Modeling` phase) could build a predictive model of follow-on likelihood using these features, rather than relying on descriptive/statistical comparisons alone.

---

## Important Visualizations

- **Follow-on Success Rate by Industry** — bar chart ranking top industries by follow-on conversion rate.
- **Seed Check Size vs. Follow-On Outcome** — log-scale boxplot comparing seed amounts for successful vs. stalled startups.
- **Geographic Variation in Follow-On Success** — bar chart of follow-on rate across the top 10 countries by volume.
- **Number of Funding Rounds vs. Follow-On Outcome** — boxplot comparing total rounds raised by outcome.
- **Time Between Seed and Latest Funding Round** — histogram of days elapsed for successful startups.
- **Multivariate Interaction: Industry × Funding Size × Outcome** — grouped bar chart of median seed funding by industry and outcome.

*(See `Code/03_EDA.ipynb` for the full, rendered set of visualizations and statistical test outputs.)*

---

## Sources

- Crunchbase startup funding dataset (company profiles, funding rounds, and amounts).
