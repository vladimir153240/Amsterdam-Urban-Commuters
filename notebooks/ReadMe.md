# Notebook Execution Guide

## Overview

This directory contains **6 sequential implementation notebooks** that reproduce the entire analysis pipeline, from raw data loading through MNL modeling and policy scenario testing.

**Total execution time**:

---

## Notebook Phases

### Phase 1: Amsterdam Data Loading & Filtering 
**File**: `implementation_notebooks/phase1_amsterdam_dataload.ipynb`

**Tasks:**
- Load ODiN 2023 `.sav` file (or Parquet alternative for implementation_notebook phase1)
- Apply 10 sequential filters (geographic, temporal, mode-based, data quality)
- Generate transformation log with row counts at each step
- Output: Harmonized Amsterdam dataset (681 rows)

**Key Outputs:**
- `amst_processed.csv` — Full Amsterdam trips
- Transformation log printed to console

**Expected Results:**
```
F1: Regular trips only (new_trip_id == 1): 1,500,000 → 1,200,000 rows
F2: Amsterdam residents (home_municipality == 363): 1,200,000 → 45,000 rows
...
F10: Unknown driving licence status (has_driving_license == 2): 1,200 → 681 rows
Final dataset: 681 rows 
```

---

### Phase 2: London Data Loading & Filtering
**File**: `implementation_notebooks/phase2_london_dataload.ipynb`

**Tasks:**
- Load 5 NTS .tab files - trip, individual, household, psu, day (load uk_data_2023.csv for implementation notebook phase2)
- Merge tables on ID keys (PSUID, IndividualID, HouseholdID)
- Filter to 2023, London region, commuting purpose, weekdays
- Apply mode and quality thresholds
- Output: Harmonized London dataset (25,188 rows)

**Key Outputs:**
- `lnd_processed.csv` — Full London trips
- Merge diagnostics

**Expected Results:**
```
PSU 2023: 3,500 → 800 PSU IDs for London
Trip 2023: 350,000 → 45,000 trips for London
Merged: 45,000 rows

Mode distribution:
  PT:  1,783 trips (70.8%)
  Car: 1,328 trips (52.7%)
  Bike: 283 trips (11.2%)
```

---

### Phase 3: Exploratory Data Analysis
**File**: `implementation_notebooks/phase3_eda_amst_lnd.ipynb`

**Tasks:**
- Descriptive statistics for both cities
- Mode distributions and visualizations(bar charts)

**Key Outputs:**
- Distribution plots (modes, age, income, distance, time)
- Summary statistics table
- Trip characteristics by city & mode

**Expected Results:**
```
Amsterdam:
  - Average trip time: 24.3 min
  - Average distance: 4.8 km
  - Bicycle share: 59.8%
  
London:
  - Average trip time: 37.1 min
  - Average distance: 8.2 km
  - PT share: 70.8%
```

---

### Phase 4: MNL Model Estimation
**File**: `implementation_notebooks/phase4_mnl_modelling.ipynb`

**Tasks:**
- Prepare choice sets (baseline alternatives)
- Specify model functional forms:
  - Base Model: Socioeconomic factors only
  - Full Model: + Trip characteristics + Peak hour
- Estimate MNL coefficients
- Calculate t-statistics, p-values, model fit (McFadden's R²)
- Generate coefficient tables

**Key Outputs:**
- `amst_final_beta.csv` — Amsterdam MNL coefficients
- `lnd_final_beta.csv` — London MNL coefficients
- Model comparison table (AIC, LL, R²)

**Expected Results:**

```
               AMS_coeff  AMS_se    AMS_t   AMS_p  LDN_coeff  LDN_se    LDN_t   LDN_p
Name                                                                                 
asc_bike          4.9413  0.7169   6.8926  0.0000    -0.1825  0.5166  -0.3532  0.7240
asc_pt           -0.4052  1.0344  -0.3918  0.6952     1.2888  0.2941   4.3817  0.0000
b_age_bike       -0.3226  0.0720  -4.4836  0.0000    -0.2590  0.0491  -5.2743  0.0000
b_age_pt         -0.3230  0.1074  -3.0065  0.0026    -0.5340  0.0338 -15.7894  0.0000
b_dist_bike      -0.3892  0.0383 -10.1525  0.0000    -0.1477  0.0121 -12.2005  0.0000
b_gender_bike    -0.4910  0.2219  -2.2130  0.0269    -0.1456  0.1399  -1.0410  0.2979
b_gender_pt       0.5460  0.3041   1.7956  0.0726     0.7449  0.0830   8.9749  0.0000
b_income_bike     0.1356  0.0812   1.6708  0.0948     0.3425  0.0518   6.6069  0.0000
b_income_pt      -0.2485  0.1145  -2.1702  0.0300     0.0536  0.0274   1.9542  0.0507
b_time_bike       0.0537  0.0109   4.9189  0.0000     0.0222  0.0048   4.6340  0.0000
b_time_pt         0.0560  0.0109   5.1172  0.0000     0.0377  0.0030  12.5953  0.0000
```

---

### Phase 5: Hypothesis Testing 
**File**: `implementation_notebooks/phase5_hypothesis_testing.ipynb`

**Tasks:**
- **Wald tests Function**: Amsterdam vs. London independency
  - H0: Association between Amsterdam and London behavior
  - H0: No association between two cities
- **Travel Time Sensitivity**:
  - Commuters sensitivity to travel time
- **Income Effect**:
  - Income effect on Commuters mode choice 

**Expected Results:**
```
H1 — TRAVEL TIME SENSITIVITY: Wald Tests

b_time_pt:
AMS = +0.0560 (SE=0.0109)
LDN = +0.0377 (SE=0.0030)
Difference = +0.0183
W = 1.6155, p = 0.106204
Decision: FAIL TO REJECT H0

b_time_bike:
AMS = +0.0537 (SE=0.0109)
LDN = +0.0222 (SE=0.0048)
Difference = +0.0315
W = 2.6426, p = 0.008227
Decision: REJECT H0 → significant difference

H2 — INCOME EFFECT: Wald Tests

 b_income_pt:
 AMS = -0.2485 (SE=0.1145)
 LDN = +0.0536 (SE=0.0274)
 Difference = -0.3021
 W = -2.5656, p = 0.010300
 Decision: REJECT H0 → significant difference

 b_income_bike:
 AMS = +0.1356 (SE=0.0812)
 LDN = +0.3425 (SE=0.0518)
 Difference = -0.2068
 W = -2.1479, p = 0.031724
 Decision: REJECT H0 → significant difference
```

---

### Phase 6: Scenario Testing
**File**: `implementation_notebooks/phase6_mnl_scenario_testing.ipynb`

**Tasks:**
- **Scenario 1**: What if bike travel time decreases 20% in London?
  - Predict new mode shares
  - Calculate bike ridership increase
- **Scenario 2**: What if there is 5euro congestion tax in Amsterdam?
  - Simulate shift from car to PT/bike

**Key Outputs:**
- Scenario comparison (before/after mode shares)

**Expected Results:**
```
CENARIO 1  London Cycle Infrastructure  (20% distance reduction)
  Aggregate Mode Shares  (n=1,000)
  Mode      Before   After   Shift
  Car        36.8%   36.1%   -0.8%
  Pt         54.3%   53.3%   -1.0%
  Bike        8.9%   10.6%   +1.7%

  Equity Impact by Income Quintile
  Quintile             Car        PT      Bike
  Q1 (n=149)       -0.44%    -0.49%    +0.94%
  Q2 (n=133)       -0.44%    -0.58%    +1.03%
  Q3 (n=142)       -0.77%    -0.76%    +1.52%
  Q4 (n=217)       -0.85%    -0.99%    +1.83%
  Q5 (n=359)       -0.98%    -1.51%    +2.49%

SCENARIO 2  Amsterdam Congestion Charge  (€5/day)
  Aggregate Mode Shares  (n=500)
  Mode      Before   After   Shift
  Car        25.1%   24.9%   -0.2%
  Pt         13.5%   13.8%   +0.3%
  Bike       61.4%   61.3%   -0.1%

  Equity Impact by Income Quintile
  Quintile             Car        PT      Bike
  Q1 (n= 62)       +0.00%    +0.00%    +0.00%
  Q2 (n= 63)       -0.12%    +0.16%    -0.04%
  Q3 (n= 80)       -0.30%    +0.40%    -0.10%
  Q4 (n=130)       -0.20%    +0.34%    -0.14%
  Q5 (n=165)       -0.35%    +0.46%    -0.11%
```

---

## How to Run

Sequential Execution (Recommended)


Open and run in browser:
1. phase1_amsterdam_dataload.ipynb
2. phase2_london_dataload.ipynb
3. phase3_eda_amst_lnd.ipynb
... etc

**Run cells sequentially** (don't skip cells). Each builds on previous results.

## Outputs Explained

### CSV Files (in `data/processed/`)

| File | Rows | Columns | Purpose |
|------|------|---------|---------|
| `amst_processed.csv` | 681 | 31 | Harmonized Amsterdam trips |
| `lnd_processed.csv` | 25,188 | 28 | Harmonized London trips |

### Model Results (in `data/model_results/`)

| File | Content |
|------|---------|
| `amst_final_beta.csv` | MNL coefficients, t-stats, p-values (Amsterdam) |
| `lnd_final_beta.csv` | MNL coefficients, t-stats, p-values (London) |

---

## Dependencies Versions

```python
pandas>=1.3.0
numpy>=1.21.0
scipy>=1.7.0
statsmodels>=0.13.0
scikit-learn>=1.0.0
matplotlib>=3.4.0
seaborn>=0.11.0
pyreadstat>=1.1.0  # For .sav files (optional)
biogeme >= 3.3.2
jupyter>=1.0.0
```

## Data Flow Summary

```
Phase 1 (Amsterdam)         Phase 2 (London)
    ↓                           ↓
Raw data (122 MB)           Raw data (1.65 GB)
    ↓                           ↓
Filter & standardize        Filter, merge & standardize
    ↓                           ↓
amst_processed.csv          lnd_processed.csv
(681 rows, 31 cols)         (25,188 rows, 28 cols)
    ↓                           ↓
    └───────────┬───────────┘
                ↓
          Phase 3: EDA
          (Visualizations)
                ↓
          Phase 4: MNL Models
          (Coefficients)
                ↓
          Phase 5: Hypothesis Tests
          (Significance)
                ↓
          Phase 6: Scenarios
          (Policy Analysis)
```


## Support & Questions

- **Data access issues?** → See `data/raw/README.md`
- **Model interpretation?** → See Phase 4-5 notebooks + comments
- **Reference material?** → See root `README.md` (References section)

---

**Last Updated**: April 2026  
**Notebooks**: 6 phases, ~50 min total runtime  
**Reproducibility**: ✅ Full transparency, all code provided
