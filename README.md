# SQL Retail Sales Analysis Project

## Project Overview

This project demonstrates the application of SQL for analyzing retail sales data. It covers database creation, table design, data cleaning, exploratory data analysis, and answering key business questions using SQL queries. The dataset simulates typical retail transaction records with customer demographics and sales details.

---

## Database and Table Setup

```sql
CREATE DATABASE Sales_Analysis;
USE Sales_Analysis;

CREATE TABLE retail_sales
(
    transaction_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);

SELECT * FROM retail_sales LIMIT 10;


## Data Validation & Cleaning

- Checked for missing or NULL values in critical columns:
  - `transaction_id`
  - `sale_date`
  - `sale_time`
  - `gender`
  - `category`
  - `quantity`
  - `cogs`
  - `total_sale`

SELECT * FROM retail_sales WHERE
    transaction_id IS NULL OR
    sale_date IS NULL OR
    sale_time IS NULL OR
    gender IS NULL OR
    category IS NULL OR
    quantity IS NULL OR
    cogs IS NULL OR
    total_sale IS NULL;

-- Exploratory Data Analysis (EDA)

-- Total number of sales transactions
SELECT COUNT(*) AS total_sales FROM retail_sales;

-- Total unique customers
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;

-- List unique product categories
SELECT DISTINCT category FROM retail_sales;

-- Business Questions and SQL Analysis

-- Q1: Retrieve all sales from 2022-11-05
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';

-- Q2: Transactions in 'Clothing' category with quantity > 4 in Nov 2022
SELECT * FROM retail_sales 
WHERE category = 'Clothing' 
AND quantity > 4
AND sale_date >= '2022-11-01' 
AND sale_date < '2022-12-01';

-- Q3: Total sales and total orders by category
SELECT category,
       SUM(total_sale) AS net_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

-- Q4: Average age of customers who bought 'Beauty' products
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';

-- Q5: Transactions where total sale > 1000
SELECT * FROM retail_sales
WHERE total_sale > 1000;

-- Q6: Number of transactions by gender and category
SELECT category,
       gender,
       COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;

-- Q7: Best selling month by average sale per year
SELECT year, month, avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank_num
    FROM retail_sales
    GROUP BY year, month
) AS ranked_sales
WHERE rank_num = 1
ORDER BY year, month;

-- Q8: Top 5 customers by total sales
SELECT customer_id,
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

-- Q9: Number of unique customers per category
SELECT category,
       COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;

-- Q10: Number of orders by shift (Morning, Afternoon, Evening)
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT shift,
       COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;

```

## Conclusion


This project provides a foundational approach to retail sales data analysis using SQL. It includes database setup, data cleaning, exploratory queries, and answering relevant business questions that offer insights into sales trends, customer demographics, and product category performance

