# Coffee Lot Quality and Grading Tracker

**Student:** NDAGIJIMANA RASANA Bonte
**Student ID:** 29506
**Course:** INSY 8311: Database Development with PL/SQL
**Instructor:** Eric Maniraguha
**University:** Adventist University of Central Africa (AUCA)
**Tools:** Oracle Database 21c Express Edition, SQL Developer, Oracle APEX

---

## 1. Problem Statement

After coffee is harvested, each lot is tested by tasting it. Coffee people call this cupping. The taster gives scores for things like aroma, flavor, acidity and body, and the scores are added up to give a grade.

In many places these scores are written on paper or kept in different Excel files. This causes problems:

- The same score can get different grades from different people.
- Mistakes, such as a score that is too high or a wrong total, are not noticed.
- Managers cannot easily see which regions or processing methods give the best coffee, so weak lots are found too late.

## 2. Objectives

**Main objective:** Build an Oracle database with PL/SQL that keeps coffee lots and their taste scores, gives each lot a grade automatically, and shows quality results.

**Specific objectives:**
1. Design a clean 3NF database for lots, regions, varieties, processing methods and scores.
2. Clean and load the real coffee data (1,339 lots).
3. Use PL/SQL to grade lots and refuse wrong scores.
4. Keep an audit log of every change and raise alerts for low-quality lots.
5. Build an Oracle APEX app where tasters enter scores and managers see dashboards.

## 3. Data Source

- **Dataset:** Coffee Quality database from CQI (by Diego Volpatto)
- **Link:** https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi
- **Original source:** Coffee Quality Institute, gathered in January 2018
- **File I will use:** `merged_data_cleaned.csv`
- **Size:** 1,339 lots and 44 columns, from 36 countries (1,311 Arabica and 28 Robusta)
- **Licence:** Database: Open Database, Contents: Database Contents (as shown on Kaggle)
- **Personal data:** I will remove the owner, farm, mill and company columns, so no personal information is loaded.

**Things to clean:** one lot has a total score of 0, altitude is in metres for some rows and feet for others, and some values are missing (variety, processing method, altitude, region). Only one lot is from Rwanda, so I will focus on the 160 lots from East Africa.

## 4. Innovation

- **Option:** User interface
- **Tool:** Oracle APEX, connected to my Oracle database

The taster enters scores in an APEX form, and the form calls my PL/SQL procedure `record_cupping`. A dashboard shows average quality by country, region and processing method, and the open alerts. The rules stay in PL/SQL, and APEX only shows and collects the data.

## 5. Planned Tables

| Table | What it keeps |
|---|---|
| `countries` | Countries of origin |
| `regions` | Regions inside each country |
| `varieties` | Coffee varieties |
| `processing_methods` | How the coffee was processed |
| `partners` | Organisations that graded the lots |
| `lots` | One row for each coffee lot |
| `cupping_scores` | The ten taste scores, total and grade of each lot |
| `grade_rules` | Grade names and the minimum score for each |
| `app_users` | App users and their roles |
| `quality_alerts` | Alerts for low-quality lots |
| `audit_log` | Who changed what, and when |

One region has many lots, and one lot can have many scores.

## 6. Planned PL/SQL Features

| Feature | Where I will use it |
|---|---|
| Function `get_grade` | Gives the grade of a lot from its total score |
| Procedure `record_cupping` | Saves a new score, adds up the total, sets the grade and creates an alert if the score is low |
| Procedure `register_lot` | Adds a new lot after checking its region, variety and method |
| Cursor | Goes through each region to find its average score for reports |
| Trigger 1 | Stops scores outside 0 to 10 |
| Trigger 2 | Writes every insert, update and delete on scores to the audit log |
| Trigger 3 | Stops changes to a lot that is already final |
| Package `coffee_pkg` | Keeps the functions and procedures together |
| Exceptions | Handle an unknown lot or a duplicate score |

## 7. References

- Volpatto, D. (n.d.). *Coffee quality database from CQI* [Data set]. Kaggle. https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi
