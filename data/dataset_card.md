# Dataset Card — Coffee Quality Database

## 1. Dataset Overview

| Item | Description |
|---|---|
| Dataset Name | Coffee Quality Database from CQI |
| Dataset Creator | Diego Volpatto |
| Original Data Source | Coffee Quality Institute (CQI) |
| Hosting Platform | Kaggle |
| Original Collection Date | January 2018 |
| File Format | CSV (Comma-Separated Values) |
| Main Dataset | `merged_data_cleaned.csv` |
| Number of Records | 1,339 |
| Number of Columns | 44 |
| File Size | 619,413 bytes (approximately 605 KB) |
| Licence | Database Contents License (DbCL) v1.0 |
| Licence Provider | Open Data Commons |

**Dataset Source:**  
https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi

**Licence Reference:**  
https://opendatacommons.org/licenses/dbcl/1-0/

The Database Contents License (DbCL) v1.0 grants rights over the contents, subject to its conditions. Section 2.2 requires compliance with the Open Database License (ODbL).

## 2. Dataset Description

The Coffee Quality Database contains information about coffee samples collected from the Coffee Quality Institute (CQI).

The dataset provides information about coffee origins, producers, farms, processing methods, physical characteristics, and sensory quality assessments.

It includes information about coffee species, particularly Arabica and Robusta, together with quality evaluation results.

Each row represents an individual coffee sample or lot and its associated information and quality assessment.

The dataset will be used as the foundation for developing the Coffee Lot Quality and Grading Tracker database.

## 3. Dataset Files

The downloaded dataset contains three CSV files:

| Filename | Number of Records | Description |
|---|---:|---|
| `merged_data_cleaned.csv` | 1,339 | Combined dataset containing Arabica and Robusta records |
| `arabica_data_cleaned.csv` | 1,311 | Dataset containing Arabica coffee records |
| `robusta_data_cleaned.csv` | 28 | Dataset containing Robusta coffee records |

### Selected Main Dataset

The main dataset selected for Phase II is:

`merged_data_cleaned.csv`

This file was selected because it contains both Arabica and Robusta coffee records, providing a combined dataset for data-quality assessment, entity identification, normalization, and logical database design.

The other two CSV files are retained as supporting source files.

## 4. Data Quality Assessment

An initial inspection was performed on `merged_data_cleaned.csv` to understand its structure and identify potential data-quality problems before normalization.

The inspection produced the following results:

| Data Quality Indicator | Result |
|---|---:|
| Total Records | 1,339 |
| Total Columns | 44 |
| Completely Duplicated Rows | 0 |
| Columns Containing Missing Values | 19 |
| Total Missing Cells | 4,044 |

### 4.1 Missing Values

The following missing values were identified:

| Column Name | Missing Records |
|---|---:|
| Lot.Number | 1,063 |
| Farm.Name | 359 |
| Mill | 318 |
| Color | 270 |
| Producer | 232 |
| altitude_mean_meters | 230 |
| altitude_high_meters | 230 |
| altitude_low_meters | 230 |
| Altitude | 226 |
| Variety | 226 |
| Company | 209 |
| Processing.Method | 170 |
| ICO.Number | 159 |
| Region | 59 |
| Harvest.Year | 47 |
| Owner | 7 |
| Owner.1 | 7 |
| Quakers | 1 |
| Country.of.Origin | 1 |
| **Total** | **4,044** |

These missing values will be considered during the data-cleaning and normalization stages.

### 4.2 Duplicate Records

The initial inspection identified zero completely duplicated rows across all 44 columns.

This means that no two records were identical across every column. However, further investigation may still be required to identify duplicate coffee samples or repeated identifiers.

### 4.3 Inconsistent Data Formats

Several fields require additional inspection because their values may use different formats or conventions.

| Column | Observation |
|---|---|
| Altitude | Values include different measurement units, including metres and feet. |
| Harvest.Year | Values include different representations, such as years and year ranges. |
| Bag.Weight | Values use different measurement units, including kg and lbs. |
| Grading.Date | Date values are stored as text. |
| Expiration | Date values are stored as text. |
| Region | Some values may use different spellings or naming conventions. |
| Color | Values include Green, Bluish-Green, and Blue-Green. |

### 4.4 Additional Observations

The following observations were also identified:

- The `Unnamed: 0` column appears to represent an imported row index.
- The `Owner` and `Owner.1` columns require investigation because they appear to contain similar information.
- The `Total.Cup.Points` column contains one record with a score of zero.
- The altitude unit information contains 1,157 records marked as metres (m) and 182 marked as feet (ft).

These observations will be examined further before deciding how the fields should be represented in the relational database.

## 5. Data Handling and Preparation

The original downloaded CSV files are stored in the following repository location:

`data/raw/`

The original files will be preserved without modification to maintain the integrity of the source data.

The merged dataset will be used for the main data analysis and normalization process.

Data-quality issues identified during the initial inspection will be documented before any cleaning or transformation takes place.

Any derived or processed dataset will be kept separate from the original raw files.

## 6. Privacy and Anonymization

The dataset contains fields relating to owners, producers, farms, and companies.

These fields will be reviewed to determine whether anonymization is necessary in accordance with the assignment requirements.

Where anonymization is required, it will be applied to the derived dataset rather than modifying the original raw CSV files.

## 7. Intended Use in the Project

The dataset will support the development of the Coffee Lot Quality and Grading Tracker.

It will be used to:

1. Identify the main entities involved in coffee quality tracking.
2. Identify relationships between coffee samples, producers, origins, processing methods, and quality assessments.
3. Demonstrate normalization from Unnormalized Form (UNF) to First Normal Form (1NF), Second Normal Form (2NF), and Third Normal Form (3NF).
4. Develop a logical Entity Relationship Diagram (ERD).
5. Prepare a data dictionary describing the proposed database tables and attributes.
6. Provide a foundation for the later implementation of the relational database.

## 8. References

1. Volpatto, D. *Coffee Quality Database from CQI*. Kaggle.  
   https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi

2. Coffee Quality Institute (CQI). Original source of the collected coffee quality data.

3. Open Data Commons. *Database Contents License (DbCL) v1.0*.  
   https://opendatacommons.org/licenses/dbcl/1-0/
