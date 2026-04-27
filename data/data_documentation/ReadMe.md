# Data Documentation

## Overview

This folder contains **metadata, codebooks, and variable mappings** for the two original datasets used in this project:
- **Amsterdam (ODiN 2023)**: Dutch national travel survey
- **London (NTS 2023)**: UK national travel survey

These files are essential for understanding variable definitions, response codes, and the translation between Dutch and English column names.

---

## Files in This Folder

### 1. `ODiN2023_Codeboek_v1.0.tab`
**Source**: Centraal Bureau voor de Statistiek (CBS)  
**Language**: Dutch + English translations in header

**Purpose**: Complete codebook for the ODiN 2023 survey.

**Key Content**:
- Variable names (original Dutch)
- Variable definitions (Dutch)
- Response codes and category meanings
- Data types (numeric, categorical, date)
- Sampling and weighting information

**How to Use**:
1. Open in Excel or text editor
2. Search for variable name (e.g., `KHvm` for main transport mode)
3. Cross-reference response codes with your data values
4. Example: `KHvm = 1.0` → "Personenauto - bestuurder" (Car - driver)

**Size**: ~500 KB | **Format**: Tab-delimited

---

### 2. `column_mapping.json`
**Created By**: Data preparation pipeline  
**Language**: JSON (English keys, Dutch-to-English mappings)

**Purpose**: Programmatic mapping of original column names to standardized English names.

**Structure**:
```json
{
  "KHvm": "mode_class",
  "Reisduur": "travel_time_min",
  "AfstV": "distance_hm",
  "Leeftijd": "age",
  "Geslacht": "gender",
  ...
}
```

**How to Use**:
```python
import json

with open('column_mapping.json', 'r') as f:
    mapping = json.load(f)

# Rename Amsterdam columns programmatically
df_amst = df_amst.rename(columns=mapping)
```

**Size**: ~5 KB | **Format**: JSON

---

### 3. `english_column_labels.txt`
**Created By**: ODiN metadata extraction (`meta.column_labels`)  
**Language**: English

**Purpose**: Human-readable English translation of all ODiN column names extracted from the SPSS file metadata.

**Format**: One column name per line, in order matching the original data file.

**How to Use**:
1. Load the Amsterdam raw data
2. Read this file line-by-line to get English names in order
3. Assign to DataFrame columns:
   ```python
   with open('english_column_labels.txt', 'r') as f:
       english_names = [line.strip() for line in f.readlines()]
   
   df_amst.columns = english_names
   ```

**Size**: ~20 KB | **Format**: Text file (UTF-8)

**Note**: This file was generated automatically from SPSS metadata and may contain encoding issues. Use `column_mapping.json` for production code.

---
### 4. `Main Table Variables London.csv`
**Source**: UK Data Service (Study 5340)  
**Language**: English

**Purpose**: Complete data dictionary for NTS 2002-2024 variables with availability timeline.

**Key Content**:
- Variable table location (Household, Individual, Trip, Stage, etc.)
- Variable name and description
- Data type (int, decimal, datetime)
- **Availability matrix**: Shows which years (2002-2024) each variable was collected
- Notes on variable changes over time

**How to Use**:
1. Open in Excel (or use `pd.read_csv()` in Python)
2. Search for variable name (e.g., `MainMode_B04ID`)
3. Check the "2023" column: `1` = available, `0` = not available
4. Read "Notes" column for any data quality alerts
5. Example: `MainMode_B04ID = 3` → "Car driver"

**Size**: ~450 KB | **Format**: CSV

**Critical Note**: The "Multicoded tables" sheet shows variables that were rotated (asked in some years but not others). For 2023-only analysis, check if your variable is available in 2023.

---

### 5. `5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx`
**Source**: UK Data Service  
**Language**: English

**Purpose**: Lookup tables for NTS response codes across 23 years (2002-2024).

**Content**:
- Multiple sheets (one per variable category):
  - `Age_B04ID`: Age band codes (1-9)
  - `Sex_B01ID`: Gender codes (1-2)
  - `DrivLic_B02ID`: Driving license codes (1-3)
  - `MainMode_B04ID`: Transport mode codes (1-13)
  - `TripPurpose_B04ID`: Trip purpose codes (1-8)
  - ...and many others

**How to Use**:
```python
import pandas as pd

# Read the MainMode sheet
mode_lookup = pd.read_excel(
    '5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx',
    sheet_name='MainMode_B04ID'
)

# Create a mapping dictionary
mode_dict = dict(zip(mode_lookup['Code'], mode_lookup['Label']))

# Decode your data
df_lnd['mode_name'] = df_lnd['MainMode_B04ID'].map(mode_dict)
```

**Size**: ~2 MB | **Format**: Excel (.xlsx)

**Warning**: This file has multiple sheets. Use `sheet_name` parameter in pandas to access specific lookups.

---

## How These Files Work Together

**Data Preparation Pipeline:**
```
Raw ODiN .sav file
        ↓
(Read with pyreadstat)
        ↓
Use ODiN2023_Codeboek_v1.0.tab to understand codes
        ↓
Use english_column_labels.txt to rename columns
        ↓
Use column_mapping.json to standardize names
        ↓
↓ Harmonized DataFrame ↓
        ↓
Ready for filtering & analysis
```

**Raw NTS .tab files**
        ↓
(Read with pd.read_csv)
        ↓
Use Main Table Variables London.csv to understand structure
        ↓
Use 5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx
to decode response codes
        ↓
↓ Harmonized DataFrame ↓
        ↓
Ready for filtering & analysis
```

---

## Key Variables Used in This Project

### Amsterdam (ODiN 2023)
| Variable | Codebook Location | Used For |
|----------|-------------------|----------|
| `WoGem` | ODiN Codebook | Filter: Amsterdam municipality (363) |
| `MotiefV` | ODiN Codebook | Filter: Commuting trips (1.0) |
| `KHvm` | ODiN Codebook | Main transport mode class |
| `Hvm` | ODiN Codebook | Detailed mode (bus/tram/metro breakdown) |
| `Reisduur` | ODiN Codebook | Travel time (minutes) |
| `AfstV` | ODiN Codebook | Distance (hectometers) |
| `Leeftijd` | ODiN Codebook | Age (years) |
| `Geslacht` | ODiN Codebook | Gender |
| `HHGestInkG` | ODiN Codebook | Household income (deciles) |
| `OPRijbewijsAu` | ODiN Codebook | Has car license |
| `HHAuto` | ODiN Codebook | Number of household cars |

### London (NTS 2023)
| Variable | Lookup Table Sheet | Used For |
|----------|-------------------|----------|
| `PSUStatsReg_B01ID` | `Region` | Filter: London (8) |
| `TripPurpose_B04ID` | `TripPurpose_B04ID` | Filter: Commuting (1) |
| `MainMode_B04ID` | `MainMode_B04ID` | Transport mode choice |
| `TripTravTime` | - | Travel time (minutes) |
| `TripDisIncSW` | - | Distance (miles) |
| `TripStartHours` | - | Departure hour |
| `Age_B04ID` | `Age_B04ID` | Age band (9 categories) |
| `Sex_B01ID` | `Sex_B01ID` | Gender |
| `HHIncQIS2023Eng_B01ID` | - | Income quintile (2023 England) |
| `DrivLic_B02ID` | `DrivLic_B02ID` | Driving license status |
| `NumCarVan` | - | Number of household vehicles |

---

## How to Access This Documentation in Code

```python
import pandas as pd
import json

# Load ODiN codebook
odin_codebook = pd.read_csv('ODiN2023_Codeboek_v1.0.tab', sep='\t')

# Load NTS variable dictionary
nts_vars = pd.read_csv('Main Table Variables London.csv')

# Load column mapping
with open('column_mapping.json', 'r') as f:
    col_map = json.load(f)

# Load NTS lookup tables
mode_lookup = pd.read_excel(
    '5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx',
    sheet_name='MainMode_B04ID'
)

print(f"Amsterdam codebook: {odin_codebook.shape[0]} variable definitions")
print(f"London variable list: {nts_vars.shape[0]} variables")
print(f"Column mappings: {len(col_map)} Dutch → English")
print(f"NTS mode codes: {mode_lookup.shape[0]} categories")
```

---

## References

- **ODiN 2023**: https://www.cbs.nl/en-gb/our-services/methods/surveys/korte-onderzoeksbeschrijvingen/odin
- **NTS 2023**: https://www.ukdataservice.ac.uk/find/5340
- **CBS Codebook**: Available in `ODiN2023_Codeboek_v1.0.tab`
- **NTS User Guide**: `5340_nts_data_extract_user_guide.pdf` (in raw data archive)

---

## Notes

✅ **All files are self-contained** — No internet access needed after initial download  
✅ **All codebooks are in English** — Except ODiN codebook (Dutch, but with English translations)  
✅ **All variable codes documented** — Cross-reference any response code with these files  
✅ **Reproducibility** — These files are the official source; do not modify  

If a variable code seems unclear, consult:
1. **Amsterdam**: Search `ODiN2023_Codeboek_v1.0.tab`
2. **London**: Search `Main Table Variables London.csv`, then use `5340_nts_lookup_table_response_levels_eul_2002_to_2024.xlsx`
