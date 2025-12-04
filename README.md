# Retail Sales & Holiday Performance Analysis (SQL)

## 1. Background & Overview
Holiday seasons can significantly impact retail revenue. The goal of this project was to analyze retail transaction data using SQL to:

- Identify top- and under-performing holidays
- Understand customer and product behavior
- Reveal opportunities to boost weak holiday sales

The analysis focuses on **total sales (`SUM(Total_Amount)`)** as the primary KPI while also considering **average sale per transaction (`AVG(Total_Amount)`)** to explain spending behavior.

---

## 2. Data Structure & Cleaning Summary
### Tables Used:
- `retail_holiday`
- `#retail_sales_aligned`
- `holiday_calendar`
- `#master_temp` (combined master table)

### Key Fields:
- `Total_Amount`, `Product_Category`, `Product_id`, `Customer_ID`, `Gender`, `Age`, `holiday_name`, `aligned_date`

### Cleaning & Enrichment Steps:
- Corrected numeric and bit columns
- Standardized dates and holiday flags
- Shifted retail sales dates to 2016
- Created master table combining holiday windows, aligned sales, and enrichment factors:
  - `holiday_multiplier` for each holiday
  - `uplifted_sales` = Total_Amount * holiday_multiplier
  - `petrol_factor` adjustment for order demand
  - `adjusted_order_demand` considering petrol sensitivity

---

## 3. Executive Summary (Total Sales Ranking)

| Holiday | Total Sales (SUM) | Avg Sale per Transaction (AVG) | Notes / Business Story |
|---------|-----------------|-------------------------------|-----------------------|
| Thanksgiving | $X | $Y | Strong traffic, high revenue |
| Independence Day | $X | $Y | Moderate sales, high pre/post activity |
| Christmas | $X | $Y | Low total (stores closed), opportunity for pre/post-holiday promotions |

**Story:** Thanksgiving and Independence Day drive the most revenue. Christmas shows a gap due to closures, highlighting potential for boosting pre- and post-holiday sales with targeted promotions.

---

## 4. Insights Deep Dive

### A. Holiday Performance
**Insight:** Some holidays generate high total sales; others underperform.  
**SQL Snippet:**
```sql
SELECT holiday_name,
       SUM(Total_Amount) AS total_sales,
       AVG(Total_Amount) AS avg_sale
FROM #master_temp
WHERE holiday_name IS NOT NULL
GROUP BY holiday_name
ORDER BY total_sales DESC;

Takeaway: Total sales identify revenue-heavy holidays; average sale explains customer behavior. Pre/post-Christmas promotions can capture spending that is otherwise missed.

B. Promotions
Insight: Non-promoted days often have higher average sales.
SQL Snippet:
SELECT Promo,
       SUM(Total_Amount) AS total_sales,
       AVG(Total_Amount) AS avg_sale
FROM #master_temp
GROUP BY Promo
ORDER BY total_sales DESC;

Takeaway: Blanket promotions may dilute transaction value; targeted discounts could improve overall revenue.

C. Customer Segmentation
Insight: Age and gender influence product category purchases.
SQL Snippet:
SELECT age_group,
       Product_Category,
       SUM(order_count) AS orders
FROM #age_gender_temp
GROUP BY age_group, Product_Category
ORDER BY orders DESC;

Takeaway: Marketing campaigns can be tailored by demographic to boost holiday sales.

D. Product-Level Insights
Insight: Best-selling products differ by category and age group.
SQL Snippet:
WITH product_sales AS (
  SELECT Product_Category,
         Product_id,
         SUM(ISNULL(Total_Amount,0)) AS total_sales
  FROM #master_temp
  GROUP BY Product_Category, Product_id
)
SELECT * FROM product_sales
ORDER BY total_sales DESC;

Takeaway: Adjust stock allocation during peak holidays to maximize revenue.

5. Recommendations
Pre/Post-Christmas promotions: Capture lost sales due to store closures.
Refine promotions: Target high-value products and demographics to increase average order value.
Targeted inventory & marketing: Use customer and product insights to prioritize high-potential items.
Monitor weaker holidays: Track total sales to evaluate the success of interventions.
