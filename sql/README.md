# Telco Customer Churn — BigQuery SQL Analysis

This folder contains all SQL code used to analyse the Telco Customer Churn dataset using **Google BigQuery**.  
The analysis explores churn patterns, customer segmentation, revenue loss, and service usage to support data-driven retention strategies.

---

## 📌 Dataset Information

Dataset: **IBM Telco Customer Churn**  
Rows: **7,043**  
Target variable: **Churn (BOOLEAN)**  
Tools used: **Google BigQuery**

Table name used in queries:

`neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

---

## 📁 Contents

1. Overview metrics (total customers, churn breakdown)  
2. Churn segmentation by:  
   - Contract type  
   - Internet service  
   - Tenure buckets  
   - Payment method  
   - Streaming services  
3. Service usage vs churn  
4. Revenue loss analysis  
5. High-value churners  
6. Senior vs non-senior segmentation  

---

# ----------------------------------------------------------
# 1️⃣ Overview Queries
# ----------------------------------------------------------

## Query 1 — Total Customers

```sql
SELECT COUNT(*) AS total_customers
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`;
```

---

## Query 2 — Churn Breakdown (TRUE vs FALSE)

```sql
SELECT 
  Churn,
  COUNT(*) AS customers,
  ROUND(
    COUNT(*) * 100.0 / (
      SELECT COUNT(*) 
      FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
    ),
    2
  ) AS percentage
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY Churn;
```

---

# ----------------------------------------------------------
# 2️⃣ Churn Segmentation
# ----------------------------------------------------------

## Query 3 — Churn by Contract Type

```sql
SELECT 
  Contract,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY Contract
ORDER BY churn_rate DESC;
```

---

## Query 4 — Churn by Internet Service

```sql
SELECT 
  InternetService,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY InternetService
ORDER BY churn_rate DESC;
```

---

## Query 5 — Churn by Tenure Buckets

```sql
SELECT
  CASE
    WHEN tenure <= 12 THEN '0–12 months'
    WHEN tenure <= 24 THEN '13–24 months'
    WHEN tenure <= 48 THEN '25–48 months'
    ELSE '49+ months'
  END AS tenure_group,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY tenure_group
ORDER BY tenure_group;
```

---

## Query 6 — Avg Monthly Charges by Churn Status

```sql
SELECT 
  Churn,
  ROUND(AVG(MonthlyCharges), 2) AS avg_monthly_charges
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY Churn;
```

---

# ----------------------------------------------------------
# 3️⃣ Service-Level Analysis
# ----------------------------------------------------------

## Query 7 — Churn by Payment Method

```sql
SELECT
  PaymentMethod,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY PaymentMethod
ORDER BY churn_rate DESC;
```

---

## Query 8 — Add-on Services vs Churn

```sql
SELECT 
  OnlineSecurity,
  OnlineBackup,
  DeviceProtection,
  TechSupport,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport
ORDER BY churn_rate DESC;
```

---

## Query 9 — Avg Total Charges (Lifetime Revenue)

```sql
SELECT 
  Churn,
  ROUND(AVG(TotalCharges), 2) AS avg_total_charges
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY Churn
ORDER BY Churn;
```

---

# ----------------------------------------------------------
# 4️⃣ Streaming Services & Bundling
# ----------------------------------------------------------

## Query 10 — Streaming Services vs Churn

```sql
SELECT 
  StreamingTV,
  StreamingMovies,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY StreamingTV, StreamingMovies
ORDER BY churn_rate DESC;
```

---

## Query 11 — Total Number of Add-ons vs Churn

```sql
SELECT
  (
    CAST(OnlineSecurity AS INT64) +
    CAST(OnlineBackup AS INT64) +
    CAST(DeviceProtection AS INT64) +
    CAST(TechSupport AS INT64) +
    CAST(StreamingTV AS INT64) +
    CAST(StreamingMovies AS INT64)
  ) AS num_services,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY num_services
ORDER BY num_services;
```

---

# ----------------------------------------------------------
# 5️⃣ Revenue & Customer Value
# ----------------------------------------------------------

## Query 12 — Monthly Revenue Lost Due to Churn

```sql
SELECT
  COUNT(*) AS churned_customers,
  ROUND(SUM(MonthlyCharges), 2) AS total_monthly_revenue_lost,
  ROUND(AVG(MonthlyCharges), 2) AS avg_monthly_charge_per_churned_customer
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
WHERE Churn = TRUE;
```

---

## Query 13 — High-Value Customers Who Churned

```sql
WITH avg_total AS (
  SELECT AVG(TotalCharges) AS avg_total_charges
  FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
)
SELECT
  c.customerID,
  c.Contract,
  c.tenure,
  c.MonthlyCharges,
  c.TotalCharges,
  TRUE AS is_high_value_churner
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn` c
CROSS JOIN avg_total a
WHERE c.Churn = TRUE
  AND c.TotalCharges > a.avg_total_charges
ORDER BY c.TotalCharges DESC
LIMIT 100;
```

---

# ----------------------------------------------------------
# 6️⃣ Demographic Segmentation
# ----------------------------------------------------------

## Query 14 — Senior vs Non-Senior Churn by Contract Type

```sql
SELECT
  CASE 
    WHEN SeniorCitizen = 1 THEN 'Senior'
    ELSE 'Non-senior'
  END AS senior_group,
  Contract,
  COUNT(*) AS total_customers,
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
GROUP BY senior_group, Contract
ORDER BY senior_group, churn_rate DESC;
```

---

# End of SQL Analysis
