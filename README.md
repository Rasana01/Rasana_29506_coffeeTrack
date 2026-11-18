# Coffee Lot Quality and Grading Tracker

- **Student:** NDAGIJIMANA RASANA Bonte
- **Student ID:** 29506
- **Course:** INSY 8311: Database Development with PL/SQL
- **Instructor:** Eric Maniraguha
- **University:** Adventist University of Central Africa (AUCA)
- **Tools:** Oracle Database 21c Express Edition, SQL Developer, Oracle APEX

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

- National Agricultural Export Development Board. (2026). *Rwanda's agricultural exports surpass $1 billion, marking a historic milestone*. NAEB. https://www.naeb.gov.rw/1/updates/news-detail/rwandas-agricultural-exports-surpass-1-billion-marking-a-historic-milestone
- Oracle. (n.d.). PL/SQL triggers. In *Oracle Database PL/SQL language reference* (Release 21). https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/plsql-triggers.html
- Sudrajat, A. R., Pramunendar, R. A., & Rohman, M. S. (2025). Optimized LightGBM model for predicting total cup points of Arabica coffee using sensory cupping data. *Jurnal Teknologi dan Manajemen Informatika, 11*(2), 143-152.
- TechnoServe. (2024, January 22). *Introducing a new mobile application for monitoring coffee farming best practices* [Blog post]. https://www.technoserve.org/blog/mobile-application-coffee-farming-best-practices/
- Teran, E. (2024). Enhancement of coffee quality attributes by combining processing methods and varieties. *Beverages, 10*(1), Article 10. https://doi.org/10.3390/beverages10010010
- Volpatto, D. (n.d.). *Coffee quality database from CQI* [Data set]. Kaggle. https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi

---

## 8. Phase I – Business Process Modeling

### Process Description

The Coffee Cupping and Quality Grading process explains how coffee lots are assessed and graded before they are approved for the market. Cuppers retrieve the coffee lot information, assess qualities such as aroma, flavor, acidity and body, and record the assessment scores in the Coffee Tracker system.

The system checks for duplicate assessments and validates the submitted scores. If the scores are invalid, the cupper corrects them and resubmits the assessment. Once the scores are valid, the system calculates the total score and assigns a grade. It then checks whether the grade meets the required quality threshold. If the lot meets the threshold, the final grading result is saved in the Coffee Tracker database. If it does not meet the threshold, the lot is referred to the Quality Manager for further review.

### Modeling Tool

- **Tool used:** draw.io (diagrams.net)
- **Diagram type:** Business Process Model and Notation (BPMN)

### Process Modeling Documents

- **Published process diagram:** (https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=Coffee_Cupping_Quality_Grading_Diagram.drawio&dark=auto#R%3Cmxfile%3E%3Cdiagram%20name%3D%22Page-1%22%20id%3D%22ESy60fq7bCtZewSS65gw%22%3E7V1td6K6Fv41%2FVgX7%2BjHVtu5c1d7pmfsPXN6v9wVJVVukTgQq86vP0kgAiEgKiid0tWZSggBkr2f%2FeydnXilDxebLwFYzh%2BRA70rTXE2V%2FroStNUfaCTP7RkG5UMVDUqmAWuE1dKCsbuLxgXKnHpynVgmKmIEfKwu8wWTpHvwynOlIEgQOtstVfkZe%2B6BDOYKxhPgZcv%2FeE6eB6V9jU7Kf8XdGdzfmfVGkRnFoBXjt8knAMHrVNF%2Bt2VPgwQwtGnxWYIPdp5vF%2Bi6%2B4Lzu4eLIA%2BrnLBj%2F%2F%2BAA%2B%2Fvn9By2%2F6%2F6Cx%2Frl5ebiWtBIXhXjL%2ByBcuwsP%2BOTodjp3PecBbNGK1g4xmL7xo9sAhmT4nnhTqlD0CKhQKKR0jgL3F%2FIx8OJqpJ0Ax2OvZWuM6S34dXjBr3glJ%2BMLVNKTt0sYuAuIYTBegqnrz0i5QYpBMOW1bHIYvdk78Fbxm11plkcffUrklnye0c9D9PoKyZEyXC2XtCkiSL5D%2Fv9zBTwXb8knIusOOxNdTfo83UB8FxhguEl1aTwqXyAiDxrQZuYpwTHNWEzWiZSpuh4Xxu3wOlyX4kMQy%2Fhs13QiBuRDLAkHSIWWl4oy6ZGLSsmg7h%2FP3EjRwYDBUV2rDsq6Nu5Lram%2BNCv2pZbryyVyfcwex7wlv0pPNUhjw93fK5PUGrIz0XG2rG%2FKa6usVGyB147%2FCm2rQhl%2FklztbNvkl8jBHCzpGy02M2oiepPlwu%2FBd9YbaRGgo%2BoS4H0AE%2Bg9odDFLvLJuQnCGC1IBY%2BeuCVwMAvQyneGyEMB6yn9lf2k2rjx3Bm9FqMlRYH4aEruSWQohRakkAwhsSXwKSm6JXDmuT4c7kwKFVYQLqODV3cDnaRWBIO%2BAwJaGG4XE0TfaAZ9GFBJjx471Tx7WccNSGvRC%2FoooGJZjE5UOTKiYf1cIX7iOmRqc0MqmMsNa4afziGah2g7AQTOltnCgBrOCOV6KTCL7ncCmOl5hROQTNA%2Boynt4zQCOjkzX1Ef0SqYwpJ6Vk5v6b3G8SEdWjRDPvDuktJbJr9UiJhkJXUeEJVXJiD%2FhxhvY0QEK4yyugI3Lv479fmFNkX0LjoacUPLDrapg7QY0jIHvoKVhx%2FkmhVxo1iAgeNEdpX0qGLsJIOA%2FAyW9eNALi4B9AB237ODUvvwW%2FWBrybDWFmZnS%2BMIFfbj8NRmS1CsC3DYFmZJimUNimzAcJDFoM3BuEbNdLruYshJVy0xpqcZ4xvirnw78T8NoxNu2D1p5QNEAwNdqK8pFQrks0p8jywDN0Ja4uW0Ps%2BguCN1QeTEAdgiusATqsAOIm6EJBcUC6YJ4U3YQjDcEEEq1bsNOyemUNPO4ueaj8Ln7rKxq0RFerXCKBft39%2Bmz34%2FX%2B%2Fvf01cMfPP51HyAnSmQHUJ33zN0dGepCCUHqYYCg72qaPLoCi1iVRdNCh6GdG0axvexiy2gXI%2Bh1OUeBk8HRMSmBIZdFPA%2B4zeTz6rEUQW58LXgV5tTMir24fC72DUkiF3gSt02gqOt8ZnHTuXY877MylTx2Ts%2FF1luDw02MHhHOG0Wp88AQwAU2flUToRxq4oUE65gUlUQOhLIv2p6BtEewbLDx0MPBXBG%2FdkuhWZThPRy2sEj%2BJNUd6DmxTFWIATu72RAsSebaz4qz2e7YtCG7UpPxyK3t5nyFk%2BgEj6x9flQ5OCg3t4i%2BFLUW9nGuJadauC09QtuNdBSm6OwADbSeISTRjF4PYG%2B7IRS3kwQ1uFMys4kqjDo1A%2FKAo6iBhyimYbxzOrXwQwsiCuSHGAGtAcinFPZQyJ7JFXlgIk8noArHh6A1yCIxR85XgtFC0V6QWruNICPbJDpaqFAnJ97ub57uGY0%2BWEHtSs8Nu1xB8kg67VaPrVBZbvkzsKYk3vaTOXCr2VOZaHmd3C21szniphmBFrWOtoLWnofqMYFl%2FdY7e53T0qBlhs5o1u3o4cOE7TDt1D2wi4qtPo2uAEZ%2B9fEB8uIYcvL7ZhH8n1TZuqTp1%2B5zq1gjpRgF9R2lgpR0BFNHjVGtgX3KPrqJ21ZfEcDJNLhpVaTxMGW9DDBd1cudqmRKNjZhalR%2FrHSD%2BjoBYVU36hWqSBlZJO%2FlL%2FiIusQMwrT5eTRYuxqQDJJCZxsazT%2FdpioCZWq8GTiJXQaNTwc%2Bsgk3M9QyBN115kZI9I2o5c%2BykJeTEaCwLUK3qWnd61elVRb0a0WDagqYgpuhhNkm5LYplCqxfs5uzYEfPmnLV2xd15TbyY6b8fbTMlwg5z5D6Io%2FRHB3DryxN%2FUtK06WD%2BKfG4g%2Bd77YG2fgez6Qrisjn5scVQaTKZsdF0BPZRcPRfD2WrHhpFJhw%2BZLEHUuFXxBNNthZcTpgYpGuCAKJJc2yG2roXX9OhAw%2Fs5nPKHfkZJRJWeQ%2FUGUrJxXF%2BJJrpafqlpUZYB6orTBzxKsQsx3CRhIaOK7UxzhF2lhGOqtQzF3dHMssJJT76GMxU5wR%2F2MNtkJ604XWnARztJiswr1rTpIlJjw7ii8viY9n6%2Bcte1e4mXqrkMnqGVM%2FivKmR4gl8uE5ZPATU9MFpJIel%2F7c8VQ8J5gwZ%2Bsy789AVyXr7MwsWbVE3NZzyUj1sdVB4%2Fyiy6%2BuwjL7Z2KZ8gm%2B2rG6iw58luhAUTB8DNiU%2B73rs4Dbbsmy8h2GRJlaEhiw43TTs%2BRTV50RrKBl3VLcdi7FhX5mFW4UICPUL0nFvWRKbF4JlWghBJ10agHzGQjMRzcaUkVD4oJWVEUuwzTBOsR07kAO8FXm5ZvgvpKJ%2BhF51AkIzxGMlSQ9C2PcHwjstqn0Ju3oIe6SnutPeu4Lc8h2PU6NdOSNpn0a7cIxUzsdNN3FSY8IwSf%2B0Ev6XMuco4g4XSoEr9ZI27pAVhfIOmSK9YamJsQRqzCVHxTytarvNH%2BoLYErMVOvjkQ9%2BcRAnVGrUqXv5ljPEv0yLgnw3Bk4fp5Kvt75Y89TbWFYGSb2TFRphq0Xj12TM1Py8daa5oc8kbhDj3OsslMvSg%2FN40MJHT3s6OFJ9PBrNMvp0j%2BA%2FHNWS490Ect0ZZvrrfw18CPK6ATgFbeFKQrrTPWmIgGm2jRR5C%2F2e0G93QKsL7GsF8N67USmaMqJwcdmivLlu398O33x7D5eafeNLI60iGWatYeNujn1zz6nPoIeZMZ9lDL0I2bYs7vctGJuXRW2C69lqa1c1ySrxkqVsv2TPaO7hzs6R9PIZIywAU0dux%2FLu9v8venXqdnrH83TNi%2FraVunsi95KuLHZl8vNcXplJ6q9dUsLrSJTtn5Qe6Sp7rkqfrJV1HylIxnKREjc1oRVBH3hlWV%2FC6W9YHx0ftyt3xhWntNckkqyqVMsqWeapLlg9yZ5DjEoQxs7UibzBvJXn8tNNCcxbbqnGar6s19boQoYU6XWlpSdT%2BXFu2CpaoFDCDZ2%2BAR%2BGRoglr3vir9Aradp97UBiGGfqy28t0I9tlz%2FaKu%2Bgf4piL9XDsNlA5P5fHu4tVdvHqPy%2FQdvrtwfUX3W11fJ%2Bh54xFobEmQ2qg%2FFFqi3PWoVxe%2F6OIXxcpYtDAoq4PRDsj3HpjNWHpI9GWM9wRHSLXVshUBDdXSM8qpNxjPUI%2BOZ3Dmuo%2F%2FlH8pTtMzFRn6Y5%2BX%2F9S63qSq16Mc7m8Ti97ncebadvzJReXEr8kW6g9Oqs4n%2F4qqC9uLC9X37Cck3ks0lg3vJ3S8iyJR0RZm%2FWdnEw9f1lWLiqpXpwc10vPixCqrE6AyG5idRCdnFMW6u7mvrtT6EUp9TV6irwz2aLXkOzXMnmLT5aqqaVmaxVMMuFqaZ5b9%2BPZHB1oNuWh%2F7EBrTXtpKT3d6me30tq379tZ1yscvQdgVdy7bGjmZG6S4hfaZRe0CshnKWDAvtkvh3zayLYUOZ05Pe81QT7dtPp18xlxS0K7nHGIyUaH1ic%2FsRcvJSm1bVYoSdfMx6RbmixW284A%2F3kanf%2Fr0IQQjFb2JZMFPh45DBB9k0QkaIzhkfiotMY%2F%3C%2Fdiagram%3E%3C%2Fmxfile%3E)
- **Business process diagram (PDF):** [Coffee_Cupping_Quality_Grading_Diagram.pdf.pdf](docs/business%20process/Coffee_Cupping_Quality_Grading_Diagram.pdf.pdf)
- **Business process explanation (PDF):** [Coffee_Cupping_Quality_Grading_Explanation.pdf](docs/business%20process/Coffee_Cupping_Quality_Grading_Explanation.pdf)

