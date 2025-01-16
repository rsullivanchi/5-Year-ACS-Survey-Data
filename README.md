# 5-Year-ACS-Survey-Data
This repository contains two notebooks that will guide the user to create a flat file of selected variables from the ACS 5 Year Survey

Below is a draft for a `README.md` file that provides an overview and explanation for all the code you’ve shared during our discussion:

---

# Data Processing Pipeline for Census Tract and Ward Data

This repository contains Python scripts to process and aggregate census tract data into ward-level summaries. The scripts utilize raw JSON data and apply transformations for readability, consolidation, and analysis.

## Table of Contents

1. [Overview](#overview)
2. [Key Features](#key-features)
3. [Dependencies](#dependencies)
4. [Code Breakdown](#code-breakdown)
    - [Variable Grouping](#variable-grouping)
    - [Functions](#functions)
    - [Main Execution Flow](#main-execution-flow)
5. [Output](#output)

---

## Overview

The pipeline processes raw census data stored in JSON format, aggregates demographic variables, and outputs ward-level summaries in a structured format. It automates data extraction, consolidation, and rounding, providing a CSV file for analysis.

---

## Key Features

- **Variable Grouping**: Automatically combines raw variables into meaningful categories for demographics and income groups.
- **Tract Summarization**: Aggregates tract-level data into specified geometry areas (e.g., wards).
- **CSV Output**: Produces a finalized ward-level dataset with demographics and survey year information.



---

## Dependencies

The project uses the following Python libraries:
- `os`
- `json`
- `pandas`
- `census_area`
  


## Code Breakdown

### **1. Variable Grouping**
Demographic variables are combined into meaningful groups to simplify analysis. The `variable_groups` dictionary defines this mapping. For example:
```python
variable_groups = {
    'Total Population': ['B01001_001E'],
    'White': ['B01001A_001E'],
    'Black or African American': ['B01001B_001E'],
    '$50,000 to $74,999': ['B19101_011E', 'B19101_012E']
}
```
The values for keys like `'Total Population'` or `'White'` are calculated as the sum of specified sub-variables.

---

### **2. Functions**

#### **`unpack_variable_groups`**
This function flattens raw tract data based on the `variable_groups` dictionary. It:
- Iterates through the grouped variables.
- Sums the sub-variable values for each group.
- Multiplies the sums by the `%_of_tract` overlap factor for each geometry.

#### **`process_tracts_in_geo_area`**
This function processes all tracts within a specific geometry:
- Uses `unpack_variable_groups` to flatten each tract's variables.
- Aggregates results for all tracts within the geometry.
- Returns a DataFrame summarizing the geometry's demographics.

---

### **3. Main Execution Flow**

#### **File Iteration**
Iterates over JSON files in the `demo_data_by_ward_2` directory. It:
- Skips `.ipynb` checkpoint and non-JSON files.
- Opens and processes each file, logging errors where applicable.

#### **Concatenation**
All processed geometries are concatenated into a single DataFrame:
```python
ward_frame = pd.concat(ward_summaries, axis=0, ignore_index=True)
```

#### **Rounding**
Numerical columns are rounded for clarity:
```python
for column in ward_frame.columns:
    if column not in ['Ward', 'Community Area']:
        ward_frame[column] = ward_frame[column].apply(lambda x: round(x, 0))
```

#### **CSV Output**
The resulting DataFrame is saved as `ward_summary_2023.csv` with the `Survey_Year` column set to 2023.

---

## Output

The script generates a CSV file with ward-level summaries. Example structure:

| Ward     | Community Area | Total Population | White | Black or African American | ... | Survey_Year |
|----------|----------------|------------------|-------|----------------------------|-----|-------------|
| Ward_001 | CA_01          | 12345           | 5678  | 4321                       | ... | 2023        |
| Ward_002 | CA_02          | 54321           | 8765  | 1234                       | ... | 2023        |

---

## Troubleshooting

- **Missing Files**: Ensure JSON data is correctly placed in the `<working>` directory.
- **Corrupt Data**: The script will log and skip invalid files, but ensure file contents match the expected structure.

---

This `README.md` provides a detailed walkthrough of the project, ensuring users can set up, run, and interpret results effectively. Let me know if you'd like me to adjust this further!
