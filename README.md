# Coffee Lot Quality and Grading Tracker

| | |
|---|---|
| **Student** | NDAGIJIMANA RASANA Bonte |
| **Student ID** | 29506 |
| **Course** | INSY 8311: Database Development with PL/SQL |
| **Instructor** | Eric Maniraguha |
| **Institution** | Adventist University of Central Africa (AUCA), Faculty of Information Technology |
| **Academic year** | 2026-2027, Semester I |
| **Repository** | Rasana_29506_coffeeTrack |
| **Environment** | Oracle Database 21c Express Edition (XE), SQL Developer; Oracle APEX for the user interface |

---

## 1. Problem Statement

Coffee cooperatives and washing stations grade each lot by "cupping" it (a structured taste test scored on aroma, flavor, acidity, body and more). In many places these scores are written on paper or kept in scattered spreadsheets. This causes three problems:

- **Inconsistent grading:** the same total score can be graded differently by different people.
- **Invalid data goes unnoticed:** scores outside the allowed range, or totals that do not match their components, are not caught.
- **No quality overview:** managers cannot easily compare quality by region, altitude, variety or processing method, so low-quality lots are discovered late.

## 2. Objectives

**Main objective:** Build an Oracle PL/SQL database that records coffee lots and cupping scores, grades each lot automatically with business rules, and reports on quality.

**Specific objectives:**
1. Design and implement a normalized (3NF) Oracle schema for lots, regions, varieties, processing methods and cupping scores.
2. Clean, load and validate the real CQI coffee quality data (1,339 lots).
3. Enforce grading and validation rules in PL/SQL (functions, procedures, packages and triggers).
4. Record every change to scores in an audit log, with alerts for suspicious or low-quality lots.
5. Build an Oracle APEX application where cuppers enter scores and managers view quality dashboards.

## 3. Data Source

| | |
|---|---|
| **Dataset** | Coffee Quality database from CQI |
| **Author on Kaggle** | Diego Volpatto |
| **Link** | https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi |
| **Original source** | Scraped from the Coffee Quality Institute (CQI) site, January 2018 (scraper: https://github.com/jldbc/coffee-quality-database) |
| **File used** | `merged_data_cleaned.csv` (Arabica + Robusta) |
| **Size** | 1,339 records, 44 columns (1,311 Arabica, 28 Robusta) |
| **Coverage** | 36 countries, about 356 distinct regions; 160 lots from East Africa (Ethiopia, Tanzania, Uganda, Kenya, Malawi, Burundi, Rwanda, Zambia) |
| **Licence** | Database: Open Database, Contents: Database Contents (as shown on the Kaggle page) |
| **Personal data** | None loaded: owner, farm name, mill, company, producer and certification contact columns are **dropped** before loading. (Some farm-name cells in the raw file even contain phone numbers and emails.) |

**Known data issues (handled in Phase II cleaning):**
- 1 lot has a total score of 0 (invalid), and 2 grading dates are malformed.
- Altitude is recorded in metres for 1,157 rows and in feet for 182 rows; I use the `altitude_mean_meters` column (converted) and keep the unit.
- Missing values: variety (226), processing method (170), altitude (230), region (59).
- Harvest year has mixed formats (for example `2013`, `2013/2014`).
- Only 1 lot is from Rwanda, so the project is built on the global CQI data, with East African lots as the focus group for analysis.

## 4. Innovation

**Option:** User interface
**Tool:** Oracle APEX, connected directly to my Oracle database.

- A cupper enters a lot's scores in an APEX form. The form calls the PL/SQL procedure `record_cupping`, which validates the scores, computes the total and assigns the grade.
- Dashboard pages show average quality by country and region, altitude band, variety and processing method, plus a list of low-quality lots and open alerts.
- All business rules stay in PL/SQL (procedures and triggers). APEX only displays and collects data.
- Documentation, run steps and screenshots will be in the `innovation/` folder.

## 5. Planned Tables

| Table | Purpose |
|---|---|
| `countries` | Country of origin |
| `regions` | Region within a country (FK to countries) |
| `varieties` | Coffee variety (Bourbon, Caturra, SL28, ...) |
| `processing_methods` | Washed, Natural, Semi-washed, Honey, Other |
| `partners` | In-country partner organisations that graded the lot |
| `lots` | One row per coffee lot (species, region, variety, method, partner, harvest year, grading date, altitude in metres, bags, bag weight, moisture, defects) |
| `cupping_scores` | The ten sensory scores, total points and assigned grade, per lot |
| `grade_rules` | Grade names and the minimum total score for each |
| `app_users` | Application users and roles (cupper, manager); no personal data |
| `quality_alerts` | Alerts raised for low-quality or suspicious lots |
| `audit_log` | Who changed what and when, with old and new values |

Relationships: a country has many regions; a region has many lots; a lot has one or more cupping scores; variety, processing method and partner each have many lots. The full ERD and data dictionary will be added in Phase II.

## 6. Planned PL/SQL Features

| Feature | Where it is used |
|---|---|
| Function `get_grade(total_points)` | Returns Specialty, Premium or Below Grade by reading `grade_rules` |
| Function `calc_total(...)` | Sums the ten sensory scores to check the entered total |
| Procedure `record_cupping(lot_id, scores...)` | Inserts a score, calculates the total, assigns the grade and raises an alert if below the threshold |
| Procedure `register_lot(...)` | Adds a new lot with validation of region, variety and method |
| Cursor (explicit) | Loops over regions to compute average and best score per region for reports |
| Trigger (before insert/update) | Blocks any score outside its allowed range (0 to 10) and any total that does not match its components |
| Trigger (audit) | Writes every insert, update and delete on `cupping_scores` to `audit_log` |
| Restriction rule trigger | Prevents score changes on a lot after it has been locked as final |
| Package `coffee_pkg` | Groups the functions and procedures above |
| Exceptions | `NO_DATA_FOUND` for unknown lots, `DUP_VAL_ON_INDEX` for duplicate scoring, custom exceptions for invalid scores |
| Scheduled job (optional, `DBMS_SCHEDULER`) | Nightly check that raises alerts for low-quality lots |

## 7. Repository Structure

```
docs/
  research/research_log.md
  proposal/
  presentation/
data/
innovation/        (added in later phases)
sql/               (added in later phases)
```

## 8. References

- Volpatto, D. (n.d.). *Coffee quality database from CQI* [Data set]. Kaggle. https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi
