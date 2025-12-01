# Telco Customer Churn Analysis  
### BigQuery • SQL • Data Cleaning • Exploratory Analysis

This project analyses the **IBM Telco Customer Churn** dataset using **Google BigQuery**.  
It focuses on identifying drivers of customer churn, revenue impact, customer lifetime behaviour, and segmentation patterns — the type of analysis commonly performed in data analyst roles.

This project demonstrates:

- SQL querying and data transformation  
- Customer segmentation  
- KPI measurement  
- Data exploration & summarisation  
- Business insight communication  
- Clean GitHub documentation  

---

# 📦 Project Structure

```
/
├── sql/
│   └── README.md          → Full SQL code (14 queries, fully commented)
├── data/                  → Clean dataset (optional)
├── tableau/               → Visual dashboards (coming soon)
└── README.md              → Main project documentation (this file)
```

---

# 📊 Dataset Overview

**Dataset:** IBM Telco Customer Churn  
**Rows:** 7,043  
**Target variable:** `Churn` (BOOLEAN)  
**Source:** Kaggle

Dataset includes:

- Customer demographics  
- Contract type  
- Internet services subscribed  
- Monthly & total charges  
- Add-on service selections  
- Payment methods  
- Tenure (months with the company)  
- Churn status  

---

# 🚀 Tools Used

| Tool | Purpose |
|------|---------|
| **Google BigQuery** | SQL analysis, aggregation, segmentation |
| **Excel** | Initial data cleaning & validation |
| **GitHub** | Version control & documentation |
| **Tableau (upcoming)** | Visual dashboard creation |

---

# 📊 Results Summary

This section summarises all insights generated from the BigQuery analysis.  
Numbers are taken directly from SQL outputs in `/sql/README.md`.

---

## 1️⃣ Overall Churn Rate

| Metric | Value |
|--------|--------|
| Total customers | **7,043** |
| Customers who churned | **1,869** |
| Customers who stayed | **5,174** |
| **Churn rate** | **26.54%** |

📌 **Insight:**  
About **1 in 4 customers** left the service — signalling a major retention challenge.

---

## 2️⃣ Churn by Contract Type

| Contract Type | Customers | Churned | Churn Rate |
|---------------|-----------|---------|------------|
| **Month-to-month** | 3,875 | 1,655 | **42.71%** |
| One year | 1,473 | 166 | 11.27% |
| Two year | 1,695 | 48 | 2.83% |

📌 **Insight:**  
Long-term contracts drastically reduce churn.  
Month-to-month users are the most vulnerable group.

---

## 3️⃣ Churn by Internet Service

| Internet Service | Customers | Churned | Churn Rate |
|------------------|-----------|---------|------------|
| **Fiber optic** | 3,096 | 1,297 | **41.89%** |
| DSL | 2,421 | 459 | 18.96% |
| No internet | 1,526 | 113 | 7.40% |

📌 **Insight:**  
Fiber optic customers churn at **twice** the rate of DSL customers — possibly due to pricing or service quality perception.

---

## 4️⃣ Churn by Tenure (Customer Lifetime)

| Tenure Group | Customers | Churned | Churn Rate |
|--------------|-----------|---------|------------|
| **0–12 months** | 2,186 | 1,037 | **47.44%** |
| 13–24 months | 1,024 | 294 | 28.71% |
| 25–48 months | 1,594 | 325 | 20.39% |
| 49+ months | 2,239 | 213 | 9.51% |

📌 **Insight:**  
New customers (first 12 months) churn the most.  
Retention efforts should focus early in the customer lifecycle.

---

## 5️⃣ Monthly Charges vs Churn

| Churn Status | Avg Monthly Charges |
|--------------|----------------------|
| **Churned** | **\$74.44** |
| Stayed | \$61.27 |

📌 **Insight:**  
Higher-paying customers are more likely to churn — pricing may be a friction point.

---

## 6️⃣ Churn by Payment Method

| Payment Method | Customers | Churned | Churn Rate |
|----------------|-----------|---------|------------|
| **Electronic check** | 2,365 | 1,071 | **45.29%** |
| Mailed check | 1,612 | 308 | 19.11% |
| Bank transfer (auto) | 1,544 | 258 | 16.71% |
| Credit card (auto) | 1,522 | 232 | 15.24% |

📌 **Insight:**  
Electronic check users churn **the most** — encouraging AutoPay could reduce churn.

---

## 7️⃣ Add-On Services vs Churn  
(Highlights from 16 service combinations)

- Lack of security/backup/support services increases churn.
- The **highest churn rate (46.41%)** occurred among customers with:
  - OnlineSecurity = FALSE  
  - OnlineBackup = FALSE  
  - DeviceProtection = TRUE  
  - TechSupport = FALSE  
- The **lowest churn group (5.32%)** subscribed to **all** four services.

📌 **Insight:**  
Add-on services improve stickiness — bundles reduce churn significantly.

---

## 8️⃣ Total Charges (Lifetime Revenue) vs Churn

| Churn Status | Avg Total Charges |
|--------------|-------------------|
| Stayed | **\$2,549.91** |
| Churned | \$1,531.80 |

📌 **Insight:**  
Non-churned customers bring in **\$1,000+ more lifetime revenue** than churners.

---

## 9️⃣ Streaming Services & Churn

| StreamingTV | StreamingMovies | Customers | Churn Rate |
|-------------|------------------|-----------|-------------|
| true | false | 767 | 31.68% |
| false | true | 792 | 31.19% |
| true | true | 1,940 | 29.43% |
| false | false | 3,544 | **22.80%** |

📌 **Insight:**  
Streaming add-ons have only a moderate effect on churn.

---

## 🔟 Number of Services vs Churn

| # Services | Customers | Churn Rate |
|------------|-----------|-------------|
| **1 service** | 966 | **45.76%** |
| 0 services | 2,219 | 21.41% |
| 6 services | 284 | 5.28% |

📌 **Insight:**  
More services = lower churn.  
Customers with **6 services** churn dramatically less.

---

## 1️⃣1️⃣ Monthly Revenue Lost to Churn

| Churned Customers | Total Monthly Revenue Lost | Avg Monthly Charge |
|-------------------|-----------------------------|---------------------|
| 1,869 | **\$139,130.85 per month** | \$74.44 |

📌 **Insight:**  
Annual revenue lost ≈ **\$1.67 million** (recurring).  
Reducing churn yields huge financial impact.

---

## 1️⃣2️⃣ High-Value Churners (Top Examples)

These customers had **above-average total charges** yet still churned.

| CustomerID | Contract | Tenure | MonthlyCharges | TotalCharges |
|------------|----------|--------|----------------|--------------|
| 2889-FPWRM | One year | 72 | 117.80 | **8,684.80** |
| 0201-OAMXR | One year | 70 | 115.55 | 8,127.60 |
| 3886-CERTZ | One year | 72 | 109.25 | 8,109.80 |
| ... | ... | ... | ... | ... |

📌 **Insight:**  
These represent high-value customers who should have been prioritised for retention.

---

## 1️⃣3️⃣ Churn by Senior Status

| Senior Group | Contract | Customers | Churn Rate |
|--------------|----------|-----------|-------------|
| **Senior, Month-to-month** | 807 | **54.65%** |
| Senior, One year | 190 | 15.26% |
| Senior, Two year | 145 | 4.14% |
| Non-senior, Month-to-month | 3,068 | 39.57% |
| Non-senior, One year | 1,283 | 10.68% |
| Non-senior, Two year | 1,550 | 2.71% |

📌 **Insight:**  
Senior citizens on month-to-month contracts churn the most — a key demographic risk.

---

# 🧠 Skills Demonstrated

### Technical
- Complex SQL queries  
- Aggregation, CASE statements, grouping  
- Data validation and cleaning  
- Segmentation & cohort analysis  
- BigQuery schema management  

### Analytical
- KPI measurement  
- Churn modelling logic  
- Customer lifetime revenue analysis  
- Identifying behavioral drivers of churn  
- Converting numbers into insights  

### Professional
- Clean documentation  
- Project structuring  
- Markdown writing  
- Using GitHub for analytics projects  

---

# 📈 Tableau Dashboard (Coming Soon)

A Tableau dashboard will be added to visualise:

- Churn KPIs  
- Contract & tenure segments  
- Churn risk heatmaps  
- Revenue impact analysis  
- High-value churners  
- Service adoption patterns  

Folder structure:

```
/tableau/dashboard.twbx
```

---

# 📁 Full SQL Code

All SQL queries are documented with comments in:

👉 **`/sql/README.md`**

---

# ⭐ Summary

This project demonstrates:

- Strong SQL capabilities  
- Ability to analyse customer churn end-to-end  
- Evidence-based insights that support business decisions  
- Clean and professional portfolio documentation  

More portfolio projects coming soon. 🚀
