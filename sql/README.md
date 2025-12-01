-- ==========================================================
-- Query 1: Count total number of customers in the dataset
-- Purpose:
--   • Verify that the data loaded correctly
--   • Establish the total population size (used in churn rate)
-- ==========================================================

SELECT COUNT(*) AS total_customers
FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`;

-- ==========================================================
-- Query 2: Calculate churn breakdown (TRUE vs FALSE)
-- Purpose:
--   • Determine how many customers churned vs stayed
--   • Compute churn rate percentage of total population
-- Notes:
--   • Churn is a BOOLEAN field (TRUE = churned, FALSE = stayed)
--   • Subquery is used to compute percentage of total customers
-- ==========================================================

SELECT 
  Churn,                                 -- TRUE (churned) or FALSE (not churned)
  COUNT(*) AS customers,                 -- Number of customers in each churn group
  
  -- Churn percentage relative to the entire customer base
  ROUND(
    COUNT(*) * 100.0 / (
      SELECT COUNT(*) 
      FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
    ),
    2
  ) AS percentage

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY Churn;                           -- Group results by churn status

-- ==========================================================
-- Query 3: Churn rate by contract type
-- Purpose:
--   • Identify which contract types have the highest churn
--   • Understand how customer commitment (month-to-month vs 1–2 year)
--     affects retention behavior
-- Insights expected:
--   • Month-to-month contracts typically show highest churn
--   • Two-year contracts show lowest churn
-- ==========================================================

SELECT 
  Contract,                               -- Contract type (Month-to-month, One year, Two year)
  COUNT(*) AS total_customers,            -- Number of customers under each contract type
  
  -- Count how many customers churned within each contract type
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,
  
  -- Calculate churn rate percentage for each contract type
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY Contract                         -- Group results by contract type
ORDER BY churn_rate DESC;                 -- Sort highest → lowest churn rate

-- ==========================================================
-- Query 4: Churn rate by type of internet service
-- Purpose:
--   • Determine which internet service type (DSL, Fiber optic, None)
--     is associated with higher churn
--   • Understand potential quality or pricing issues in specific services
-- Insights expected:
--   • Fiber optic often shows the highest churn rate
-- ==========================================================

SELECT 
  InternetService,                         -- DSL, Fiber optic, or No service
  COUNT(*) AS total_customers,             -- Number of customers for each service type

  -- Number of customers who churned for each service type
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate percentage for each internet service type
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY InternetService
ORDER BY churn_rate DESC;                   -- Show highest churn rate first

-- ==========================================================
-- Query 5: Churn rate by tenure buckets
-- Purpose:
--   • Identify how long customers stay before they churn
--   • Group customers into lifetime buckets for easier analysis
--   • Understand whether new or long-term customers are more likely to churn
-- Insights expected:
--   • Short-tenure customers (0–12 months) often have highest churn
-- ==========================================================

SELECT
  -- Create categorical tenure buckets
  CASE
    WHEN tenure <= 12 THEN '0–12 months'
    WHEN tenure <= 24 THEN '13–24 months'
    WHEN tenure <= 48 THEN '25–48 months'
    ELSE '49+ months'
  END AS tenure_group,

  COUNT(*) AS total_customers,               -- Total customers within the bucket

  -- Number of churned customers in the bucket
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Percentage of customers who churned within each tenure group
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY tenure_group
ORDER BY tenure_group;                       -- Order buckets chronologically

-- ==========================================================
-- Query 6: Average monthly charges for churned vs non-churned customers
-- Purpose:
--   • Determine whether customers who churn are paying more each month
--   • Identify pricing pain points or affordability issues
-- Insights expected:
--   • Churned customers often have higher monthly charges
-- ==========================================================

SELECT 
  Churn,                                     -- TRUE = churned, FALSE = active

  -- Average monthly bill for each churn group
  ROUND(AVG(MonthlyCharges), 2) AS avg_monthly_charges

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY Churn;                              -- Compare churned vs non-churned

-- ==========================================================
-- Query 7: Churn rate by payment method
-- Purpose:
--   • Identify which payment methods are associated with higher churn
--   • Detect friction points (e.g., electronic check often shows high churn)
--   • Useful for understanding impact of payment convenience on retention
-- ==========================================================

SELECT
  PaymentMethod,                              -- e.g., Electronic check, Mailed check, Auto transfer
  COUNT(*) AS total_customers,                -- Number of customers using each payment method

  -- Number of customers who churned under each payment method
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate for each payment method
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY PaymentMethod
ORDER BY churn_rate DESC;                      -- Display highest churn method first

-- ==========================================================
-- Query 8: Churn rate based on key online service add-ons
-- Purpose:
--   • Understand whether customers lacking protection or support services
--     (OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport)
--     are more likely to churn
--   • These services often reduce churn by improving customer experience
-- ==========================================================

SELECT
  OnlineSecurity,                              -- TRUE = subscribed, FALSE = not subscribed
  OnlineBackup,
  DeviceProtection,
  TechSupport,
  
  COUNT(*) AS total_customers,                 -- Total number of customers in each combination

  -- Number of customers who churned in this service group
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate for each service combination
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY 
  OnlineSecurity, 
  OnlineBackup, 
  DeviceProtection, 
  TechSupport

ORDER BY churn_rate DESC;                       -- Show highest churn combinations first

-- ==========================================================
-- Query 9: Average total charges (lifetime revenue) by churn status
-- Purpose:
--   • Compare the lifetime value of churned vs non-churned customers
--   • Helps understand revenue impact of losing customers
--   • Useful for CFO / revenue retention conversations
-- Insights expected:
--   • Long-term customers who do NOT churn typically have higher TotalCharges
-- ==========================================================

SELECT 
  Churn,                                         -- TRUE or FALSE

  -- Average lifetime revenue collected from each group
  ROUND(AVG(TotalCharges), 2) AS avg_total_charges

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY Churn
ORDER BY Churn;                                  -- FALSE then TRUE for readability

-- ==========================================================
-- Query 10: Churn rate by streaming service usage
-- Purpose:
--   • Analyse whether customers using streaming services
--     (StreamingTV and StreamingMovies) churn more or less
--   • Understand if entertainment add-ons are linked to loyalty
-- ==========================================================

SELECT 
  StreamingTV,                                  -- TRUE / FALSE
  StreamingMovies,                              -- TRUE / FALSE
  
  COUNT(*) AS total_customers,                  -- Customers in each combination

  -- Number of customers who churned
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate for each streaming combination
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY StreamingTV, StreamingMovies
ORDER BY churn_rate DESC;                        -- Highest churn combinations first

-- ==========================================================
-- Query 11: Churn rate by number of additional online services
-- Purpose:
--   • Calculate how many value-added services each customer has
--     (OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport,
--      StreamingTV, StreamingMovies)
--   • See whether having more services reduces or increases churn
-- ==========================================================

SELECT
  -- Count how many of the key services a customer has subscribed to
  (
    CAST(OnlineSecurity AS INT64) +
    CAST(OnlineBackup AS INT64) +
    CAST(DeviceProtection AS INT64) +
    CAST(TechSupport AS INT64) +
    CAST(StreamingTV AS INT64) +
    CAST(StreamingMovies AS INT64)
  ) AS num_services,

  COUNT(*) AS total_customers,                  -- Customers with this many services

  -- Number of customers who churned in this service-count group
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate for each service-count level
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY num_services
ORDER BY num_services;                           -- From 0 services up to maximum

-- ==========================================================
-- Query 12: Estimate monthly revenue lost due to churn
-- Purpose:
--   • Quantify the financial impact of churn using MonthlyCharges
--   • Provide a business-friendly number for stakeholders
-- Notes:
--   • This is a simplified estimate based on the last known monthly charge
-- ==========================================================

SELECT
  -- Total number of customers who churned
  COUNT(*) AS churned_customers,

  -- Sum of their last known monthly charges
  ROUND(SUM(MonthlyCharges), 2) AS total_monthly_revenue_lost,

  -- Average monthly charge per churned customer
  ROUND(AVG(MonthlyCharges), 2) AS avg_monthly_charge_per_churned_customer

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
WHERE Churn = TRUE;

-- ==========================================================
-- Query 13: Identify high-value churned customers
-- Purpose:
--   • Find customers who churned despite having high lifetime value
--   • These represent missed retention opportunities
-- Method:
--   • Define 'high-value' as TotalCharges above the overall average
-- ==========================================================

-- Step 1: Get the overall average TotalCharges
WITH avg_total AS (
  SELECT AVG(TotalCharges) AS avg_total_charges
  FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`
)

-- Step 2: Select churned customers whose TotalCharges exceed the average
SELECT
  c.customerID,
  c.Contract,
  c.tenure,
  c.MonthlyCharges,
  c.TotalCharges,

  -- Flag all these customers as high-value churners
  TRUE AS is_high_value_churner

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn` c
CROSS JOIN avg_total a

WHERE 
  c.Churn = TRUE
  AND c.TotalCharges > a.avg_total_charges

ORDER BY c.TotalCharges DESC
LIMIT 100;                                      -- Limit for inspection and export

-- ==========================================================
-- Query 14: Churn by senior status and contract type
-- Purpose:
--   • Segment customers by age group (Senior vs Non-senior)
--   • Compare churn behavior across contract types within each segment
--   • Useful for building targeted retention strategies by age + contract
-- ==========================================================

SELECT
  -- Convert SeniorCitizen from 0/1 into a readable label
  CASE 
    WHEN SeniorCitizen = 1 THEN 'Senior'
    ELSE 'Non-senior'
  END AS senior_group,

  Contract,                                     -- Contract type

  COUNT(*) AS total_customers,                  -- Customers in this segment

  -- Count of churned customers in this segment
  SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) AS churned,

  -- Churn rate for each seniority + contract combination
  ROUND(
    SUM(CASE WHEN Churn = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*),
    2
  ) AS churn_rate

FROM `neon-glyph-479906-i0.Telco_Customer_Churn.Telco Customer Churn`

GROUP BY senior_group, Contract
ORDER BY senior_group, churn_rate DESC;
