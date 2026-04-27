# Model Results

## Overview

This folder contains **estimated MNL model coefficients** from Phase 4 of the analysis. These files store the regression outputs (beta coefficients, standard errors, t-statistics, p-values) for interpretation in Phase 5 (Hypothesis Testing) and Phase 6 (Scenario Testing).
---

The model results files can be accessed via the link: https://drive.google.com/drive/folders/1ezucTysqFbE8G4zvq4N5OdpL4HZbJFzE

## Files in This Folder

### **Amsterdam Model: `amst_final_beta.csv`**

**Size**: ~10 KB | **Rows**: Variable | **Columns**: 8

**Content**: Multinomial Logit model estimation results for Amsterdam commuting mode choice

**Choice Set**: 3 alternatives
1. Car (driver)
2. Public Transport (bus/tram/metro)
3. Bicycle

**Baseline**: Car (driver) is the baseline category

#### Interpretation Guide

**Coefficient Sign**:
- **Positive (+)**: Increases probability of choosing PT or Bicycle (vs. Car)
- **Negative (-)**: Decreases probability of choosing PT or Bicycle (increases Car choice)

---

### **London Model: `lnd_final_beta.csv`**

**Size**: ~12 KB | **Rows**: Variable | **Columns**: 8

**Content**: Multinomial Logit model estimation results for London commuting mode choice


**Choice Set**: 3 alternatives
1. Car (driver)
2. Public Transport (bus, underground, tram, other PT)
3. Bicycle

**Baseline**: Car (driver)

Note: London has ~25,188 observations (vs. 681 for Amsterdam), so much higher statistical power.


## Notes

1. Coefficients are log-odds** — Use `np.exp(coef)` for odds ratios  
2. Baseline is car driver** — Positive coef = higher PT/Bike probability  
3. Weights applied** — Results are population-representative  
4. Significance verified** — All reported effects meet α = 0.05  
