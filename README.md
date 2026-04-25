# Amsterdam-London Urban Commuters: Modal Choice Analysis

## Project Overview

This project performs a comparative modal choice analysis of urban commuters in Amsterdam (Netherlands) and London (United Kingdom) using 2023 travel survey data. We develop Multinomial Logit (MNL) models to understand how socioeconomic factors, infrastructure, and trip characteristics influence commuter mode choices (car, public transport, bicycle).

### Research Question

**What are the key determinants of commuter mode choice (car, public transport, bicycle) in Amsterdam and London, and do these determinants differ significantly between the two cities?**

### Key Objectives

- Consolidate data from two independent sources (ODiN 2023, NTS 2023)
- Harmonize trip-level data across different survey designs and geographies
- Perform exploratory data analysis (EDA) and statistical hypothesis testing
- Estimate Multinomial Logit (MNL) models for mode choice
- Test MNL attributes
- Conduct scenario testing and sensitivity analysis

---

## Data Sources

### 1. Amsterdam (ODiN 2023)
**Citation:**
```
Informatiepunt, ipwvl and Centraal Bureau voor de Statistiek (2024). 
ONDERZOEK ONDERWEG IN NEDERLAND - ODiN 2023 [data collection]. 
Version V1. DANS Data Station Social Sciences and Humanities. 
DOI: https://doi.org/10.17026/SS/FNXJEU
```

**Description:**
- Survey: ODiN (Onderweg in Nederland) 2023 — Dutch national travel survey
- Sample: 681 commuting trips from Amsterdam residents
- Geography: Amsterdam municipality (CBS code: 363)
- Period: 2023 (weekdays only, Mon–Fri)
- Modes: Car (driver), Public Transport (Bus/Tram/Metro), Bicycle
- Raw file: `ODiN2023_Databestand.sav` (~122 MB, SPSS format)

### 2. London (NTS 2023)
Citation:
```
Department for Transport (2025). 
National Travel Survey, 2002-2024 [data collection]. 
19th Edition. UK Data Service. 
SN: 5340. DOI: https://doi.org/10.5255/UKDA-SN-5340-15
```

Description:
- Survey: NTS (National Travel Survey) 2023 — UK national travel survey
- Sample: 25,188 commuting trips from London residents
- Geography: London statistical region (PSUStatsReg_B01ID = 8)
- Period: 2023 (weekdays only, Mon–Fri)
- Modes: Car (driver), Public Transport (Bus, Underground, other PT), Bicycle
- Raw files: 5 tab-delimited files (trip, individual, household, psu, day)
- Total size: ~1.65 GB (unfiltered, 2002-2024 archive)

---

## Project Structure

```
Amsterdam-London-Urban-Commuters/
├── README.md                          
├── data/
│   ├── raw/
│   │   ├── README.md                  # Data access instructions
│   │   ├── odin/
│   │   │   ├──  odin_2023_raw.parquet           # [~43 MB] Compressed version
│   │   │               
│   │   │
│   │   └── london
│   │       ├──uk_data_2023.csv             # [~26 MB] UK Data for 2023
│   │
│   ├── processed/
│   │   ├── README.md
│   │   ├── amst_processed.csv         # Harmonized Amsterdam data (681 trips)
│   │   └── lnd_processed.csv          # Harmonized London data (25,188 trips)
│   │
│   ├── sample_data/
│   │   ├── README.md
│   │   └── odin/
│   │       ├── odin_sample.csv        # 10-row sample for testing
│   │       └── odin_metadata.csv      # Column mappings & descriptions
│   │       
│   ├── sample_data_nts/
│   │   ├── README.md
│   │   └── london_sample_day_2023.csv
│   │   └── london_sample_household_2023.csv
│   │   └── london_sample_individual_2023.csv
│   │   └── london_sample_psu_2023.csv
│   │   └── london_sample_trip_2023.csv
│   │
│   ├── model_results/
│   │   ├── README.md
│   │   ├── amst_final_beta.csv        # Estimated coefficients (Amsterdam)
│   │   └── lnd_final_beta.csv         # Estimated coefficients (London)
│   │
│   └── data_documentation/
│       ├── README.md
│       ├── ODiN2023_Codeboek_v1.0.tab             # ODiN variable definitions
│       ├── Main Table Variables London.csv        # NTS variable definitions
│       ├── english_column_labels.txt              # Harmonized column names
│       └── 5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx
│
├── notebooks/
│   ├── README.md                      # Notebook execution guide
│   │
│   └── implementation_notebooks/      # Implementation notebooks for this project
│       ├── 01_phase1_amsterdam_dataload.ipynb
│       ├── 02_phase2_london_dataload.ipynb
│       ├── 03_phase3_eda_amst_lnd.ipynb
│       ├── 04_phase4_mnl_modelling.ipynb
│       ├── 05_phase5_hypothesis_testing.ipynb
│       └── 06_phase6_mnl_scenario_testing.ipynb
│
│   └── analysis_notebooks/      # Descriptive notebooks for this project
│       ├── 01_phase1_amsterdam_dataload.ipynb
│       ├── 02_phase2_london_dataload.ipynb
│       ├── 03_phase3_eda_amst_lnd.ipynb
│       ├── 04_phase4_mnl_modelling.ipynb
│       ├── 05_phase5_hypothesis_testing.ipynb
│       └── 06_phase6_mnl_scenario_testing.ipynb
│
└── src/
    └── README.md                      # Custom functions & utilities (if added)
```

---

## Quick Start

### Prerequisites

- Python 3.9+**
- Required packages: pandas, numpy, scikit-learn, statsmodels, scipy, matplotlib, seaborn, biogeme
- Optional: pyreadstat (for reading .sav files), dask (for large files)

### Data Setup

1. Download raw data (see `data/raw/README.md` for detailed instructions)
2. Place files in correct directories:
   - Amsterdam: `data/raw/odin/ODiN2023_Databestand.sav`
   - London: `data/raw/london/raw/UKDA-5340-tab/tab/*.tab`

3. Run Phase 1 & 2 notebooks to load and filter data automatically

### Reproduce Analysis

Execute notebooks in order:



| Phase | Notebook | Purpose | Duration |
|-------|----------|---------|----------|
| **1** | `01_phase1_amsterdam_dataload.ipynb` | Load ODiN 2023, apply filters |
| **2** | `02_phase2_london_dataload.ipynb` | Load NTS 2023, merge tables |
| **3** | `03_phase3_eda_amst_lnd.ipynb` | Exploratory analysis, visualizations |
| **4** | `04_phase4_mnl_modelling.ipynb` | Estimate MNL models | 
| **5** | `05_phase5_hypothesis_testing.ipynb` | Statistical tests, coefficient interpretation | 
| **6** | `06_phase6_mnl_scenario_testing.ipynb` | Policy scenarios, elasticities, VoT |

---

## Key Findings

### Amsterdam (681 commuting trips)

| Mode | Count | Share |
|------|-------|-------|
| Bicycle | 407 | 59.8% |
| Car | 180 | 26.4% |
| PT | 94 | 13.8% |

### London (25,188 commuting trips)

| Mode | Count | Share |
|------|-------|-------|
| PT | 1,783 | 70.8% |
| Car | 1,328 | 52.7% |
| Bicycle | 283 | 11.2% |

*Note: Trip-level weights applied in final models.*

---

## Methodology

### Data Harmonization

Both datasets were transformed to a **common schema** with 22 harmonized variables:

```python
shared_columns = [
    "person_id", "trip_id", "trip_purpose", "mode_detailed",
    "travel_time_min", "distance_km", "departure_hour",
    "weekday", "is_holiday", "age_band", "gender",
    "income_quintile", "has_driving_license", "n_cars_household",
    "weight_trip", "weight_person", "city", "is_peak", "chosen_mode",
    "n_transfers", "has_transfer", "n_legs"
]
```

### Statistical Methods

- Exploratory Analysis: Descriptive statistics, mode distributions, correlation analysis
- Hypothesis Testing: Chi-squared tests (mode vs. socioeconomic factors), t-tests (continuous variables), Wald Test Function
- Discrete Choice Modeling: Multinomial Logit (MNL) with 3 alternatives (car, PT, bicycle)


### Model Specifications

Base Model (Individual & Household Attributes):
- Income quintile, age band, gender, driving license, car availability

Full Model (+ Trip Characteristics):
- Travel time, distance, departure hour, is_peak

City-Specific Models:
- Separate MNL coefficients estimated for Amsterdam and London

---


### Data Documentation

- [ODiN 2023 Codebook](https://www.cbs.nl/en-gb/our-services/methods/surveys/korte-onderzoeksbeschrijvingen/odin) — Variable definitions, survey design
- [NTS User Guide](https://www.ukdataservice.ac.uk/find/5340) — NTS methodology, sampling, weighting
- [NTS Data Extract Manual](https://beta.ukdataservice.ac.uk/datacatalogue/series/series?id=2000037) — Data structure, lookup tables


## Acknowledgments

- **Centraal Bureau voor de Statistiek (CBS)** — ODiN 2023 data
- **UK Data Service** — NTS 2002-2024 archive
- **SoftUni** — Course framework & mentorship
