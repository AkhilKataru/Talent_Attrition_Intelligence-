# HR Talent Attrition Risk Dashboard

A Power BI dashboard identifying which candidates in a 19,000-person talent pool are most likely to be actively job-seeking, and what drives that intent.

![dashboard-preview](images/dashboard_overview.png)

## The 30-Second Pitch

Built an HR analytics dashboard in Power BI to identify which candidates in a 19,000-person talent pool were most likely to be actively looking for a new job, and what was driving that. City development index, career stage, and study status emerged as the strongest predictors — a nearly 4x gap between the highest- and lowest-risk groups. A composite risk-scoring layer was built to segment the population, a real data-quality defect was caught and fixed along the way, and the final deliverable is a one-page executive dashboard with KPIs and 6 visuals a retention or recruiting team can act on directly.

## Business Problem

Which candidates are most likely to be actively looking for a new job, what factors drive that intent, and how should retention/recruiting effort be prioritized across the pool?

**Who this helps:**
- **HR / People Analytics** — prioritize retention conversations
- **Learning & Development** — verify training is reaching the right people
- **Talent Acquisition** — identify receptive candidate pools
- **Leadership** — one trustworthy view instead of anecdote

## Dataset

- **Grain:** one row = one candidate/enrollee
- **Size:** 21,287 total rows (19,158 with a confirmed labeled outcome; 2,129 unscored test rows excluded from analysis)
- **Fields:** city + city development index, gender, education, experience, employer type/size, university enrollment status, training hours, recency of last job change, and outcome (job-seeking intent)

## Tools Used

`Power BI Desktop` `DAX` `Power Query (M)` `Data Profiling` `Hypothesis Testing`

## Approach

1. Connected directly to the Power BI semantic model so every number is traceable to a measure
2. Profiled every categorical field and computed job-change rate by segment to find real drivers, not guesses
3. Found and fixed a data-quality defect: `company_size` band "10-49" had been silently auto-converted to the date "Oct-49" upstream — fixed via an added, auditable column rather than overwriting the source
4. Formulated and tested 6 specific hypotheses against outcome data (see below) instead of charting everything
5. Built a governed DAX measure layer (started at ~22 measures across 4 folders, trimmed to 7 final measures actually used in visuals)
6. Built a composite 0–100 Flight Risk Score from the strongest observed drivers, and back-tested it against real outcomes before using it
7. Excluded 2,129 unlabeled rows at the **measure level** (not row deletion), since Power BI's Query Editor kept reverting row-level deletes on refresh
8. Designed a one-page dashboard: 5 KPI cards + 6 visuals, each mapped to a specific business question

## Data Cleaning Decisions

| Issue | Decision | Why |
|---|---|---|
| `company_size` "Oct-49" (Excel date corruption of "10-49") | Added a cleaned column, kept the original | Keeps the fix auditable |
| 2,129 unscored/unlabeled rows | Excluded via a base measure filter | Row-level deletes weren't durable due to a Query Editor sync issue |
| 65 blank `experience` values | Excluded from the experience chart only, kept everywhere else | No reliable field to impute from — fabricating a value would be worse than omitting one chart |
| "Not Specified" employer type (38.8% change rate — highest of any segment) | Kept as its own segment | The signal was real and informative, not noise |

## Hypotheses & Results

| Hypothesis | Result | Evidence |
|---|---|---|
| Candidates in less-developed cities are more likely to seek a job change | **Supported** (strongest driver) | 57.9% at low city-development-index vs. 13.6–16.8% at high |
| Less-experienced candidates are more likely to be job-seeking | **Supported** | 43.9% at 0–1 yrs vs. 15.3% at 20+ yrs |
| Full-time students are more likely to be job-seeking | **Supported** | 38.1% vs. 21.1% for no enrollment |
| More training hours reduce job-seeking intent | **Not supported** | Rate stays flat (~25–27%) across all training-hour buckets |

## Flight Risk Score — Validation

People flagged **high-risk** actually left **54%** of the time, versus only **14%** for those flagged low-risk — confirming the score meaningfully separates the two groups.

## Recommendations

- Use the Risk Score to prioritize retention conversations instead of treating every employee/candidate the same
- Focus extra support on candidates from less-developed cities and newer/less-experienced staff
- Stop assuming training hours reduce turnover — the data doesn't support it; redirect that budget
- Treat this dashboard as a fast, interpretable first step; a machine-learning model could follow once the business trusts this simpler version

## Repository Structure

```
hr-attrition-risk-dashboard/
├── README.md
├── dashboard/
│   └── hr_attrition_dashboard.pbix
├── reports/
│   └── hr_attrition_report.pdf
├── data/
│   └── README.md   (data source/download link — raw data not committed)
└── images/
    └── dashboard_overview.png
```

## Data

Raw candidate data is not included in this repository. See `data/README.md` for the source and download instructions.
