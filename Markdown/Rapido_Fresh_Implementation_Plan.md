# Rapido Fresh Implementation Plan

## Goal

Build the Rapido Captain Acquisition & Supply take-home from scratch as a manual, reproducible data analytics / EDA pipeline.

```text
RAPIDO RAW DATA
      ↓
1. LOAD THE DATA
      ↓
2. UNDERSTAND DATA
      ↓
3. DATA AUDIT
      ↓
4. CLEAN / PROCESS
      ↓
5. JOIN / TRANSFORM
      ↓
6. BUILD ANALYTICAL DATASETS
      ↓
7. EDA / FUNNEL ANALYSIS
      ↓
8. STATISTICAL ANALYSIS
      ↓
9. BUSINESS ANALYSIS
      ↓
10. IMPACT ESTIMATION
      ↓
11. RECOMMENDATIONS
      ↓
FINAL DELIVERABLES
```

The Candidate Brief provides seven raw CSVs, says the data is uncleaned, and allows Python, SQL, notebooks and other analytical tools. The working must run end-to-end from raw CSVs and be defensible in the live debrief.

## Fresh-start principles

- No vibe coding / black-box generated implementation.
- Write important analysis manually.
- Understand every important line before keeping it.
- Start with notebooks.
- Use SQL where SQL is natural.
- Use Python/Pandas for manipulation, statistics and visualization.
- Do not force an ML model into the assignment.
- Build transparent analytical/scenario models for impact estimation where supported.
- Keep raw data unchanged.
- Declare assumptions.
- Flag genuine data-quality issues instead of silently hiding them.
- Do not gold-plate.

## 1. Load

Raw files:

- `captains.csv` — one row per signup
- `doc_events.csv` — one row per document event
- `approvals.csv` — terminal onboarding outcome
- `activation.csv` — approved-captain post-approval activity
- `nudges.csv` — onboarding communications
- `airport_hourly.csv` — hourly marketplace state by zone
- `airport_trips.csv` — sampled airport-origin trips

First notebook: `01_load_and_understand.ipynb`.

Load the CSVs and inspect them before performing major transformations.

## 2. Understand

For every dataset establish:

- What does one row represent?
- What is the grain?
- What is the key?
- What are the important columns?
- Which columns are numeric, categorical and timestamps?
- How do the datasets relate?
- What business process does the data represent?

Important business flow:

```text
Signup
  ↓
DL
  ↓
RC
  ↓
Aadhaar
  ↓
Permit (Auto/Cab)
  ↓
Fitness
  ↓
Insurance
  ↓
Approval
  ↓
First order
```

A2O = signup → approved.

R2A = signup → first order completed.

## 3. Data audit

Check:

- shape
- duplicate rows
- duplicate keys
- missing values
- unexpected categories
- invalid numeric values
- timestamp validity and ranges
- records after the stated cutoff
- referential integrity
- logical consistency
- unusual/anomalous records

Do not assume every NULL is bad. Interpret missingness using the business meaning of the field.

Produce an audit table with:

```text
dataset | check | result | severity | interpretation | action
```

## 4. Clean / process

Prepare analysis-ready data:

- convert timestamps
- normalize categorical/document names
- create useful date/time fields
- create cohort/maturity variables
- create cutoff-related flags
- preserve raw values when anomalies need investigation
- handle missing values according to meaning

## 5. SQL layer

Connect the notebook to a lightweight local SQL database, preferably SQLite or DuckDB.

Use SQL for appropriate transformations and interview practice:

- SELECT
- WHERE
- GROUP BY
- HAVING
- CASE
- NULL handling
- JOIN
- CTEs
- date/time functions
- conditional aggregation
- window functions where useful

Flow:

```text
CSV
 ↓
Python load
 ↓
SQL database
 ↓
SQL views / transformations
 ↓
Python analysis
```

Do not add SQL complexity merely for appearance.

## 6. Build analytical datasets

Do not force all seven raw tables into one giant table.

### Captain onboarding analytical dataset

Combine relevant captain-level information from:

```text
captains
approvals
activation
processed document events
campaign exposure
```

Use this for A2O, R2A, funnel, segmentation and campaign analysis.

### Airport hourly analytical dataset

Keep zone × hour grain.

Useful derived metrics can include:

- fulfillment rate
- unfulfilled rate
- demand/supply indicators
- time-of-day fields

### Airport trip analytical dataset

Keep trip-level grain.

Useful derived metrics can include:

- cancellation
- distance
- fare
- drop-zone type
- return-fare availability

## 7. EDA / Funnel analysis

### A1 — Signup → approved funnel

Build:

```text
Signup
 ↓
DL
 ↓
RC
 ↓
Aadhaar
 ↓
Permit (Auto/Cab)
 ↓
Fitness
 ↓
Insurance
 ↓
Approved
```

Explicitly define:

- denominator
- cohort
- observation/maturity rule
- stage definitions
- treatment of in-progress cases

Calculate:

- stage volume
- conversion
- drop-off count
- drop-off percentage

Show actual volume loss, not percentages alone.

### A2 — Biggest fixable leak

Segment meaningful drop-offs by selected dimensions:

- city
- vehicle type
- acquisition channel
- device tier
- time/cohort
- document
- failure reason

Prioritize leaks that are:

1. large,
2. explicable,
3. actionable.

Estimate:

```text
current conversion
      ↓
reasonable improvement scenario
      ↓
downstream funnel effect
      ↓
additional approved captains/month
```

## 8. Statistical analysis

### A3 — Campaign

Evaluate `CAMP_WA_002`.

Determine:

```text
difference?
 ↓
size of difference?
 ↓
uncertainty?
 ↓
possible selection/confounding?
 ↓
can we call it an effect?
```

Calculate appropriate:

- outcome rates
- absolute difference
- relative uplift
- confidence interval
- statistical test where justified

Do not claim causality unless the design supports it.

Final A3 output: one deck-worthy number plus a confidence statement and limitations.

## 9. Business analysis

Translate findings into operational decisions.

For each major finding:

```text
What happened?
 ↓
Why does it matter?
 ↓
What could explain it?
 ↓
Can we act on it?
 ↓
What should we do?
```

### B1 — Airport demand/supply

Using `airport_hourly.csv`, identify:

- where shortages occur
- when they occur
- how large they are
- whether they are persistent or concentrated

### B2 — After an airport trip

Using `airport_trips.csv`, analyze:

- captain cancellations
- trip distance
- fare
- drop-zone type
- return fare within 20 minutes
- patterns related to return-fare availability

Ask whether post-trip behavior/economics changes the supply diagnosis.

### B3 — Is targeted acquisition the right intervention?

Answer plainly.

If YES:
- justify it
- size the opportunity

If NO:
- identify the better intervention
- support it with the same analytical rigor

## 10. Impact estimation

Build transparent analytical/scenario models rather than external API predictions.

Possible models:

### Funnel sensitivity

```text
current stage conversion
 ↓
change one stage
 ↓
propagate through funnel
 ↓
additional approvals
```

### Campaign scenario

Estimate incremental outcomes only to the extent supported by the campaign analysis and its uncertainty.

### Airport sizing

Where justified:

```text
unfulfilled demand
 ↓
addressable demand
 ↓
captain requirement/productivity assumption
 ↓
estimated opportunity
```

Do not create false precision. Make every assumption visible.

## 11. Recommendations

Produce three ranked recommendations.

For each:

1. What to do.
2. Why the evidence supports it.
3. Expected impact and working.
4. Cost/risk/uncertainty.
5. Metric to measure success.

Recommendations should pass the practical test: an operations/product team should know what to do next.

## 12. Notebook plan

```text
01_load_and_understand.ipynb
02_data_audit.ipynb
03_clean_and_process.ipynb
04_build_analytical_data.ipynb
05_funnel_analysis.ipynb
06_campaign_analysis.ipynb
07_airport_analysis.ipynb
08_impact_and_recommendations.ipynb
```

We do not have to preserve this exact file split if a later simplification is cleaner, but the analytical lifecycle remains fixed.

## 13. Final deliverables

### Memo — maximum 2 pages

Audience: Head of Supply.

Include:

- executive findings
- major funnel leak
- campaign conclusion
- airport conclusion
- three ranked recommendations
- impact estimates
- assumptions / what would change the answer

No code or unexplained jargon.

### Working

Must:

- run end-to-end from raw CSVs
- include notebooks/scripts as appropriate
- include SQL work
- include a short README
- reproduce important tables and figures

### Deck — maximum 6 slides

Suggested:

1. Executive summary
2. Onboarding funnel / biggest leak
3. Campaign analysis
4. Airport demand-supply diagnosis
5. Airport intervention decision
6. Three recommendations / expected impact

Target: 10 minutes, with interruptions.

## 14. Debrief readiness

Be able to explain:

### Data
- grain
- keys
- joins
- data-quality issues

### Funnel
- denominator
- cohort
- stage definitions
- biggest leak

### Statistics
- difference vs effect
- uncertainty
- confidence interval
- bias/confounding
- assumptions

### Business
- why the finding matters
- why the recommendation is actionable
- how impact was estimated
- what would change the recommendation

### Airport
- why/when the shortage occurs
- what happens after airport trips
- why targeted acquisition should or should not be used

## 15. Definition of done

The project is done when:

- every important number is reproducible from raw data
- important transformations are understandable
- assumptions are documented
- data-quality problems are surfaced
- Candidate Brief questions are answered directly
- recommendations are actionable
- memo is executive-readable
- deck tells one coherent story
- the entire analysis can be defended line by line

## 16. First session

Start from an empty project.

Do NOT start with funnel calculations, joins, statistical tests or recommendations.

Start with:

**Notebook 01 — Load & Understand**

First load one dataset, understand it, inspect the output, then progressively load the remaining datasets.

The goal is to learn the pipeline while building the actual submission.
