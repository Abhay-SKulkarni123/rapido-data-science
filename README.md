# Rapido - Captain Acquisition & Airport Supply Analysis

Data Science take home assessment for Rapido covering captain acquisition, onboarding funnel performance, acquisition campaign effectiveness and airport supply.

The accompanying Summary contains the executive level findings and recommendations. This README documents how to run the analysis, assessment coverage, analytical workflow, key outcomes, assumptions, limitations and submission contents.

---

## 1. How to Run

The notebooks are designed to be run sequentially from the repository root.

### Prerequisites

- Python 3.x
- Jupyter Notebook or JupyterLab
- pandas
- numpy
- matplotlib
- seaborn

### Clone the repository

```bash
git clone https://github.com/Abhay-SKulkarni123/rapido-data-science.git
cd rapido-data-science
```

### Install dependencies

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

### Start Jupyter

```bash
jupyter notebook
```

Open the `Notebooks/` directory and run the notebooks in the following order:

```text
01_load_and_understand.ipynb
        ↓
02_data_audit.ipynb
        ↓
03_clean_and_process.ipynb
        ↓
04_funnel_analysis.ipynb
        ↓
05_airport_supply_analysis.ipynb
```

The notebooks use relative paths to the raw CSV files in the `data/` directory, so the repository structure should be preserved.

The processing notebook creates the captain level analytical dataset used by the downstream captain acquisition analysis.

---

## 2. Assessment Scope

The assessment was divided into two parts.

### Part A - Captain Acquisition

#### A1. Build the Signup -> Approved funnel

- Define the funnel denominator and cohort
- Account for cohort maturity / censoring
- Define each onboarding stage
- Show stage volumes, conversion rates, and drop-offs

#### A2. Identify the biggest fixable leak

- Identify the largest funnel loss
- Segment the loss across relevant dimensions such as:
  - City
  - Vehicle type
  - Acquisition channel
  - Device tier
  - Document type
  - Failure reason
- Prioritize the most actionable opportunity
- Quantify potential incremental approved captains

#### A3. Evaluate `CAMP_WA_002`

- Assess whether the campaign demonstrates an approval lift
- Evaluate the evidence behind the growth claim
- Assess whether a 5× scale up is justified
- Provide an appropriate confidence / uncertainty assessment

#### A4. Recommend three actions

For each recommendation:

- Define the action
- Explain the expected impact
- Show the calculation / working
- Consider cost and risk
- Define success metrics

### Part B - Airport Supply

#### B1. Characterize airport demand supply mismatch

- Identify where the mismatch occurs
- Identify when it occurs
- Quantify the size of the problem

#### B2. Analyse post airport trip behaviour

- Examine airport origin trip outcomes
- Analyse drop zone behaviour
- Check return fare availability within 20 minutes
- Consider trip economics where relevant
- Assess whether these observations change the diagnosis

#### B3. Assess targeted airport acquisition

- Determine whether targeted acquisition of airport catchment captains is the appropriate intervention
- Size the opportunity where appropriate
- If acquisition is not the right intervention, explain the alternative

---

## 3. Assessment Coverage

| Assessment Requirement | Status | Primary Notebook / Deliverable |
|---|---|---|
| A1 - Signup -> Approved funnel | Complete | `04_funnel_analysis_clean.ipynb` |
| A2 - Biggest fixable leak & segmentation | Complete | `04_funnel_analysis_clean.ipynb` |
| A3 - `CAMP_WA_002` evaluation | Complete | `04_funnel_analysis_clean.ipynb` |
| A4 - Three ranked recommendations | Complete | `04_funnel_analysis_clean.ipynb` + Summary/Memo |
| B1 - Airport demand-supply mismatch | Complete | `05_airport_supply_analysis_clean.ipynb` |
| B2 - Post airport trip behaviour | Complete | `05_airport_supply_analysis_clean.ipynb` |
| B3 - Targeted airport acquisition decision | Complete | `05_airport_supply_analysis_clean.ipynb` + Summary/Memo |

**Analytical coverage: 7/7 assessment requirements addressed.**

---

## 4. Analysis Workflow

### Notebook 01 - Load & Understand

`Notebooks/01_load_and_understand_clean.ipynb`

- Loads all seven provided datasets
- Reviews table grain, dimensions, data types and distributions
- Checks identifiers and categorical fields
- Reviews timestamp ranges and missingness
- Establishes the initial understanding of the available data

### Notebook 02 - Data Audit

`Notebooks/02_data_audit_clean.ipynb`

- Validates primary key uniqueness and duplicates
- Checks referential integrity across datasets
- Validates document lifecycle and document sequence
- Checks Permit applicability
- Validates approval, activation and timestamp consistency
- Identifies and documents data-quality anomalies

### Notebook 03 - Clean & Process

`Notebooks/03_clean_and_process_clean.ipynb`

- Defines the observation cutoff and funnel maturity
- Creates captain level document progression features
- Summarizes document attempts and verification outcomes
- Combines captain, approval and activation information
- Produces the captain level analytical dataset used by downstream analysis

### Notebook 04 - Funnel & Campaign Analysis

`Notebooks/04_funnel_analysis_clean.ipynb`

- Builds the Signup -> Approved funnel
- Calculates stage-level conversion and drop off
- Segments funnel losses across relevant captain attributes
- Diagnoses RC verification failures and failure reasons
- Evaluates `CAMP_WA_002`
- Performs an observational campaign comparison
- Quantifies an illustrative RC recovery scenario

### Notebook 05 - Airport Supply Analysis

`Notebooks/05_airport_supply_analysis_clean.ipynb`

- Quantifies demand and supply across zones
- Identifies time periods with concentrated airport supply pressure
- Analyses terminal level hourly mismatch
- Examines airport origin trip outcomes
- Analyses drop zone behaviour and return-fare availability
- Evaluates implications for targeted airport supply interventions

---

## 5. Key Analytical Outcomes

### Captain Acquisition

The primary mature funnel cohort contains **22,757 captains**.

The largest absolute funnel loss occurs between DL and RC:

- DL cleared: **20,136**
- RC cleared: **14,647**
- RC drop off: **5,489**
- Signup -> Approved: **17.6%**

Stage-level conversion:

| Funnel Stage | Conversion |
|---|---:|
| Signup -> DL | 88.5% |
| DL -> RC | 72.7% |
| RC -> Aadhaar | 89.4% |
| Aadhaar -> Permit | 79.7% |
| Permit -> Fitness | 74.3% |
| Fitness -> Insurance | 56.9% |
| Insurance -> Approved | 90.6% |

RC verification failures are concentrated around document quality issues:

- Image blurred: **2,288**
- OCR low confidence: **1,773**
- Name mismatch: **1,124**
- Document expired: **910**
- Details not legible: **837**
- Wrong document type: **789**
- Duplicate document: **453**

Device-level analysis also shows higher RC drop-off among lower-tier devices:

- Low: **32.8%**
- Mid: **23.4%**
- High: **20.4%**

An illustrative 10% recovery of the 5,489 RC drop-offs corresponds to approximately **549 captains progressing**, or approximately **150 additional approvals** at the observed RC -> Approved conversion rate.

This is a scenario analysis and **not a causal forecast**.

### Growth Campaign

`CAMP_WA_002` contains:

- **8,673** unique exposed captains
- **8,058** delivered messages
- **3,569** clicks

The observed approval difference does not show a detectable lift and the confidence interval spans zero.

Campaign exposure was observational rather than randomized. In addition:

- **2,727** messages were sent before the captain decision
- **5,946** were sent after the decision

Therefore, the current data does not provide sufficient causal evidence to support **5× scaling**.

The recommended next step is a randomized campaign-vs-holdout experiment.

### Airport Supply

Across the marketplace:

- **457,610** requests
- **393,269** fulfilled
- **64,341** unfulfilled
- **85.9%** fulfillment

The two airport terminals account for:

- **55,058** unfulfilled requests
- Approximately **85.6%** of all unfulfilled requests

The strongest airport supply pressure occurs overnight, particularly during **21:00 - 03:00**:

- **37,875** unfulfilled requests in this window
- Hourly unfulfilled rates reach approximately **68 - 74%** around 22:00 - 02:00

Airport-origin trip analysis also shows weaker return-fare availability and higher cancellation for suburban drop-offs:

- Suburban captain cancellation: **21.0%**
- Suburban return fare within 20 minutes: **16.5%**

These post-trip observations are descriptive and are not interpreted as causal evidence.

---

## 6. Recommendations

### 1. Fix RC onboarding friction

Improve RC document capture and verification through:

- Better image capture guidance
- Pre submit image quality checks
- Immediate retake prompts
- Experience optimization for lower end devices

### 2. Run a targeted overnight airport supply pilot

Target captains who can realistically serve airport demand during **21:00 - 03:00** and test interventions around:

- Targeted captain acquisition
- Incentives
- Airport positioning
- Return trip economics

### 3. Require causal evidence before scaling acquisition campaigns

Use randomized holdout experiments to measure incremental Signup -> Approved conversion before making large scale campaign investment decisions.

---

## 7. Key Methodological Decisions

### Funnel maturity

A **14 day observation threshold** is used for the primary funnel analysis.

Captains signing up within 14 days of the data cutoff are treated as censored and excluded from the primary mature funnel cohort.

- Mature cohort: **22,757**
- Censored captains: **2,243**

### Funnel definition

The primary acquisition outcome is:

`Signup -> Approved`

The document progression is:

`DL -> RC -> Aadhaar -> Permit -> Fitness -> Insurance -> Approval`

Permit is applicable to Auto/Cab captains and not ERickshaw captains.

### Campaign evaluation

`CAMP_WA_002` is evaluated observationally.

Because campaign exposure was not randomized and campaign timing overlaps with the approval process, the observed campaign comparison is not interpreted as a causal treatment effect.

### Airport analysis

Airport supply is analysed using hourly marketplace data.

The airport trip dataset contains airport origin trips only. Therefore, post trip cancellation and return fare observations cannot establish a direct difference versus non airport trips.

---

## 8. Data Quality & Validation

The raw datasets were audited before downstream analysis.

Checks included:

- Primary key uniqueness
- Duplicate records
- Referential integrity
- Missingness
- Document event lifecycle
- Document sequence
- Permit applicability
- Approval / activation consistency
- Timestamp consistency
- Airport request arithmetic
- Numeric sanity checks

Known anomalies and missing fields identified during the audit were documented and carried forward rather than silently removed.

---

## 9. Limitations

The main analytical limitations are:

- Campaign exposure was not randomized.
- A substantial number of campaign messages were sent after the captain decision.
- Airport trip data contains airport origin trips only and does not provide a comparable non airport control group.
- The RC recovery estimate is an illustrative scenario rather than a causal forecast.
- Some source data fields contain missing or anomalous values identified during the audit.

These limitations are explicitly considered when interpreting the findings and recommendations.

---

## 10. Repository Structure

```text
Rapido/
├── data/
│   ├── captains.csv
│   ├── doc_events.csv
│   ├── approvals.csv
│   ├── activation.csv
│   ├── nudges.csv
│   ├── airport_hourly.csv
│   └── airport_trips.csv
│
├── Notebooks/
│   ├── 01_load_and_understand_clean.ipynb
│   ├── 02_data_audit_clean.ipynb
│   ├── 03_clean_and_process_clean.ipynb
│   ├── 04_funnel_analysis_clean.ipynb
│   └── 05_airport_supply_analysis_clean.ipynb
│
├── Markdown/
│   ├── CANDIDATE_BRIEF.md
│   └── Rapido_Fresh_Implementation_Plan.md
│
└── README.md
```

---

## 11. Submission Deliverables

The assessment requested the following deliverables:

### 1. Summary / Memo

A maximum 2 page executive document for the Head of Supply containing:

- Key findings
- Recommendations
- Relevant assumptions
- What would change the conclusions

### 2. Working Notebook / Scripts / Repository

A reproducible analytical workflow that:

- Runs from the provided raw CSV files
- Contains the analysis and working
- Includes a short README

### 3. Presentation Deck

A maximum **6 slide** presentation summarizing the analysis and recommendations for the live debrief.

### 4. Live Debrief

A **30 minute live discussion** consisting of:

- Approximately 10 minutes of presentation
- Approximately 20 minutes of questions and discussion

The live debrief is an interview discussion and is not an additional submission artifact.

---

## 12. Final Submission Contents

The complete submission consists of:

- **Summary/Memo**   executive findings and recommendations
- **Analysis notebooks**   complete analytical workflow
- **README.md**   run instructions, assessment coverage, analytical context, outcomes, assumptions and limitations
- **Presentation deck**   6-slide summary for the live debrief

---

## 13. Analytical Principle

The analysis prioritizes actionable business decisions while explicitly distinguishing between:

- Descriptive evidence
- Scenario analysis
- Causal claims

Where the available data does not support a causal conclusion, the limitation is stated and a controlled experiment or pilot is proposed as the next step.