# SQL_Query_Explanations



***

# 📊 Basic SQL Query Practice

## 🧩 Problem

You are given a table called `employees` with the following structure:

```sql
employees
---------
id (INT)
name (VARCHAR)
department (VARCHAR)
salary (INT)
hire_date (DATE)
```

### Sample Data

| id | name    | department | salary | hire\_date |
| -- | ------- | ---------- | ------ | ---------- |
| 1  | Alice   | HR         | 60000  | 2021-06-15 |
| 2  | Bob     | IT         | 80000  | 2020-03-10 |
| 3  | Charlie | IT         | 75000  | 2022-01-05 |
| 4  | Diana   | Finance    | 90000  | 2019-11-20 |
| 5  | Evan    | HR         | 65000  | 2023-07-01 |

***

## ❓ Question

Write SQL queries to answer the following:

1. Retrieve all columns for employees in the **IT department**
2. Get the **names and salaries** of employees earning more than **70,000**
3. Count the number of employees in each department
4. Find the **average salary** of all employees
5. Retrieve employees hired **after January 1, 2021**

***

## ✅ Solutions

### 1. Employees in the IT department

```sql
SELECT *
FROM employees
WHERE department = 'IT';
```

***

### 2. Names and salaries of employees earning more than 70,000

```sql
SELECT name, salary
FROM employees
WHERE salary > 70000;
```

***

### 3. Count of employees by department

```sql
SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

***

### 4. Average salary of all employees

```sql
SELECT AVG(salary) AS avg_salary
FROM employees;
```

***

### 5. Employees hired after January 1, 2021

```sql
SELECT *
FROM employees
WHERE hire_date > '2021-01-01';
```

***

## 🧠 Explanation

### 🔹 SELECT

The `SELECT` statement is used to **retrieve data** from a table.

* `SELECT *` returns all columns
* `SELECT column1, column2` returns specific columns

***

### 🔹 WHERE

The `WHERE` clause filters rows based on a condition.

* Example: `salary > 70000` filters only high earners
* Example: `department = 'IT'` filters by a category

***

### 🔹 GROUP BY

The `GROUP BY` clause groups rows that share the same value.

* Often used with aggregate functions (like `COUNT`, `AVG`)
* Example: Count employees per department

***

### 🔹 Aggregate Functions

These perform calculations on multiple rows:

* `COUNT()` → number of rows
* `AVG()` → average value
* `SUM()` → total
* `MIN()` / `MAX()` → smallest/largest value

***

### 🔹 Date Filtering

SQL allows filtering by date using comparison operators:

* `>` (after a date)
* `<` (before a date)

Example:

```sql
WHERE hire_date > '2021-01-01'
```

***
***

# 📊 Intermediate SQL Query Practice

## 🧩 Problem

You are working with two tables:

### `employees`

```sql
employees
---------
id (INT)
name (VARCHAR)
department_id (INT)
salary (INT)
hire_date (DATE)
```

### `departments`

```sql
departments
------------
department_id (INT)
department_name (VARCHAR)
```

***

### Sample Data

#### employees

| id | name    | department\_id | salary | hire\_date |
| -- | ------- | -------------- | ------ | ---------- |
| 1  | Alice   | 1              | 60000  | 2021-06-15 |
| 2  | Bob     | 2              | 80000  | 2020-03-10 |
| 3  | Charlie | 2              | 75000  | 2022-01-05 |
| 4  | Diana   | 3              | 90000  | 2019-11-20 |
| 5  | Evan    | 1              | 65000  | 2023-07-01 |
| 6  | Frank   | 2              | 95000  | 2021-09-18 |

***

#### departments

| department\_id | department\_name |
| -------------- | ---------------- |
| 1              | HR               |
| 2              | IT               |
| 3              | Finance          |

***

## ❓ Questions

Write SQL queries to:

1. Join employees with departments and display employee names with department names
2. Find the **highest-paid employee in each department**
3. Rank employees by salary within each department
4. Calculate the **running total of salaries** by department
5. Find employees who earn **above the company average salary**
6. Get the **second highest salary** in the company

***

## ✅ Solutions

### 1. Employees with department names (JOIN)

```sql
SELECT e.name, d.department_name, e.salary
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id;
```

***

### 2. Highest-paid employee per department

```sql
SELECT d.department_name, e.name, e.salary
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id
WHERE e.salary = (
    SELECT MAX(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
);
```

***

### 3. Rank employees by salary within each department

```sql
SELECT 
    e.name,
    d.department_name,
    e.salary,
    RANK() OVER (PARTITION BY e.department_id ORDER BY e.salary DESC) AS salary_rank
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id;
```

***

### 4. Running total of salaries by department

```sql
SELECT 
    e.name,
    d.department_name,
    e.salary,
    SUM(e.salary) OVER (
        PARTITION BY e.department_id 
        ORDER BY e.salary
    ) AS running_total
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id;
```

***

### 5. Employees earning above average salary

```sql
SELECT name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary) FROM employees
);
```

***

### 6. Second highest salary in the company

```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

***

## 🧠 Explanation

### 🔹 JOINs

Joins combine data from multiple tables using a shared key.

* `INNER JOIN` returns matching rows from both tables
* Essential for normalized databases (like your star schema work)

***

### 🔹 Subqueries

A query inside another query.

* Used for comparisons (`> AVG(salary)`)
* Can be correlated (dependent on outer query) or independent

***

### 🔹 Window Functions

Window functions perform calculations **across a set of rows without collapsing them**.

#### Common ones:

* `RANK()` → ranking with gaps
* `DENSE_RANK()` → ranking without gaps
* `ROW_NUMBER()` → unique row order
* `SUM() OVER()` → running totals

***

### 🔹 PARTITION BY

Splits data into groups for window functions (similar to `GROUP BY`, but keeps rows).

Example:

```sql
RANK() OVER (PARTITION BY department_id ORDER BY salary DESC)
```

→ Ranks employees **within each department**

***

### 🔹 ORDER BY (in window functions)

Defines how calculations progress (e.g., running total order)

***

### 🔹 Business Context

These patterns are heavily used in real analytics work:

* **Top performers by department** → performance dashboards
* **Ranking employees/customers/products** → KPI tracking
* **Running totals** → revenue trends over time
* **Above-average filtering** → anomaly detection

***

# 📊 Advanced SQL Query Practice

## 📌 Business Scenario

You are a Data Analyst at a SaaS company that sells monthly subscriptions.

Leadership wants to understand:

* how many customers are **active**
* how many customers **churned**
* what percentage of customers churned each month
* how much **monthly recurring revenue (MRR)** was lost due to churn
* which customer segments are most at risk

Your task is to write SQL queries that transform raw subscription data into **business-facing KPIs**.

***

# 🗂️ Database Schema

## 1. `customers`

```sql
customers
---------
customer_id (INT)
customer_name (VARCHAR)
signup_date (DATE)
segment (VARCHAR)
country (VARCHAR)
```

## 2. `subscriptions`

```sql
subscriptions
-------------
subscription_id (INT)
customer_id (INT)
plan_name (VARCHAR)
monthly_revenue (DECIMAL)
start_date (DATE)
end_date (DATE)   -- NULL means still active
status (VARCHAR)  -- active, canceled
```

## 3. `product_usage`

```sql
product_usage
-------------
usage_id (INT)
customer_id (INT)
usage_date (DATE)
login_count (INT)
feature_uses (INT)
```

***

# 🎯 Business Questions

Write SQL to answer the following:

1. What is the total number of **active customers** for each month?
2. How many customers **churned each month**?
3. What is the **monthly churn rate**?
4. How much **MRR was lost due to churn** each month?
5. Which customer segment has the **highest churn rate**?
6. Identify customers with **low product usage before churn**
7. Build a final KPI output table for leadership showing:
   * month
   * active\_customers
   * churned\_customers
   * churn\_rate
   * active\_mrr
   * churned\_mrr

***

# ✅ Advanced SQL Solution

> **Note:** This example uses SQL patterns commonly supported in PostgreSQL, Snowflake, and similar analytical databases.  
> If needed, date functions can be adjusted slightly for SQL Server, MySQL, or BigQuery.

***

## 1) Monthly Active Customers, Churn, and Revenue KPI Table

```sql
WITH calendar_months AS (
    SELECT DATE '2024-01-01' AS month_start
    UNION ALL
    SELECT DATE '2024-02-01'
    UNION ALL
    SELECT DATE '2024-03-01'
    UNION ALL
    SELECT DATE '2024-04-01'
    UNION ALL
    SELECT DATE '2024-05-01'
    UNION ALL
    SELECT DATE '2024-06-01'
),

subscription_monthly_status AS (
    SELECT
        cm.month_start,
        s.customer_id,
        s.subscription_id,
        s.monthly_revenue,
        s.start_date,
        s.end_date,
        CASE
            WHEN s.start_date <= (cm.month_start + INTERVAL '1 month' - INTERVAL '1 day')
                 AND (s.end_date IS NULL OR s.end_date >= cm.month_start)
            THEN 1
            ELSE 0
        END AS is_active_in_month,
        CASE
            WHEN s.end_date >= cm.month_start
                 AND s.end_date < (cm.month_start + INTERVAL '1 month')
            THEN 1
            ELSE 0
        END AS churned_in_month
    FROM calendar_months cm
    CROSS JOIN subscriptions s
),

monthly_customer_kpis AS (
    SELECT
        month_start,
        COUNT(DISTINCT CASE WHEN is_active_in_month = 1 THEN customer_id END) AS active_customers,
        COUNT(DISTINCT CASE WHEN churned_in_month = 1 THEN customer_id END) AS churned_customers,
        SUM(CASE WHEN is_active_in_month = 1 THEN monthly_revenue ELSE 0 END) AS active_mrr,
        SUM(CASE WHEN churned_in_month = 1 THEN monthly_revenue ELSE 0 END) AS churned_mrr
    FROM subscription_monthly_status
    GROUP BY month_start
),

final_kpis AS (
    SELECT
        month_start,
        active_customers,
        churned_customers,
        ROUND(
            CASE
                WHEN active_customers = 0 THEN 0
                ELSE (churned_customers * 100.0 / active_customers)
            END,
            2
        ) AS churn_rate_pct,
        active_mrr,
        churned_mrr
    FROM monthly_customer_kpis
)

SELECT *
FROM final_kpis
ORDER BY month_start;
```

***

# 🧠 Explanation

This query uses **multiple CTEs** to break a difficult business problem into manageable steps.

***

## 🔹 CTE 1: `calendar_months`

```sql
WITH calendar_months AS (...)
```

This creates a list of months we want to analyze.

Why it matters:

* KPI reporting is often done on a **monthly grain**
* creating a month table helps standardize reporting periods

***

## 🔹 CTE 2: `subscription_monthly_status`

```sql
subscription_monthly_status AS (...)
```

This is the core transformation.

It evaluates each subscription against each month and determines:

* whether the subscription was **active during that month**
* whether it **churned during that month**

### Logic used:

* A subscription is **active** if:
  * it started before the end of the month
  * and it has not ended before the month began
* A subscription is **churned** if:
  * its `end_date` falls within that month

This is a very common analytics pattern:
**turning event dates into month-level business flags**

***

## 🔹 CTE 3: `monthly_customer_kpis`

```sql
monthly_customer_kpis AS (...)
```

This aggregates the transformed rows into monthly business metrics:

* **active\_customers**
* **churned\_customers**
* **active\_mrr**
* **churned\_mrr**

This step uses:

* `COUNT(DISTINCT ...)`
* `SUM(CASE WHEN ...)`

These are common for building KPI tables from raw transactional data.

***

## 🔹 CTE 4: `final_kpis`

```sql
final_kpis AS (...)
```

This calculates the final derived metric:

```sql
churn_rate_pct = churned_customers / active_customers
```

This is a classic example of turning operational counts into an executive KPI.

***

# 📉 2) Churn Rate by Customer Segment

Leadership also wants to know which customer segment is churning the most.

```sql
WITH customer_subscription_status AS (
    SELECT
        c.segment,
        s.customer_id,
        s.start_date,
        s.end_date,
        CASE
            WHEN s.end_date IS NOT NULL THEN 1
            ELSE 0
        END AS churned_flag
    FROM subscriptions s
    JOIN customers c
        ON s.customer_id = c.customer_id
),

segment_churn AS (
    SELECT
        segment,
        COUNT(DISTINCT customer_id) AS total_customers,
        COUNT(DISTINCT CASE WHEN churned_flag = 1 THEN customer_id END) AS churned_customers
    FROM customer_subscription_status
    GROUP BY segment
)

SELECT
    segment,
    total_customers,
    churned_customers,
    ROUND(
        CASE
            WHEN total_customers = 0 THEN 0
            ELSE churned_customers * 100.0 / total_customers
        END,
        2
    ) AS churn_rate_pct
FROM segment_churn
ORDER BY churn_rate_pct DESC;
```

***

# 🧠 Explanation

This query joins `customers` and `subscriptions` to measure churn by **business segment**.

This is valuable because leaders often ask:

* Are enterprise customers churning less than SMB?
* Is one segment driving most of the lost revenue?
* Should retention efforts be targeted by segment?

***

# ⚠️ 3) Identify Low-Usage Customers Before Churn

This is where SQL starts feeling more like real-world analytics.

We want to identify customers who churned and had **low product engagement in the 30 days before churn**.

```sql
WITH churned_customers AS (
    SELECT
        customer_id,
        end_date AS churn_date
    FROM subscriptions
    WHERE end_date IS NOT NULL
),

usage_before_churn AS (
    SELECT
        cc.customer_id,
        cc.churn_date,
        AVG(pu.login_count) AS avg_logins_last_30_days,
        AVG(pu.feature_uses) AS avg_feature_uses_last_30_days
    FROM churned_customers cc
    LEFT JOIN product_usage pu
        ON cc.customer_id = pu.customer_id
       AND pu.usage_date BETWEEN cc.churn_date - INTERVAL '30 day'
                             AND cc.churn_date - INTERVAL '1 day'
    GROUP BY cc.customer_id, cc.churn_date
)

SELECT
    customer_id,
    churn_date,
    COALESCE(avg_logins_last_30_days, 0) AS avg_logins_last_30_days,
    COALESCE(avg_feature_uses_last_30_days, 0) AS avg_feature_uses_last_30_days,
    CASE
        WHEN COALESCE(avg_logins_last_30_days, 0) < 3
         AND COALESCE(avg_feature_uses_last_30_days, 0) < 5
        THEN 'High Risk - Low Usage'
        ELSE 'Normal'
    END AS risk_flag
FROM usage_before_churn
ORDER BY churn_date DESC;
```

***

# 🧠 Explanation

This query combines **behavioral usage data** with churn outcomes.

That is powerful because it moves beyond simple reporting and starts answering:

* **Why are customers churning?**
* Are we seeing product disengagement before cancellation?
* Can we build a churn-risk model later?

This is a great portfolio example because it shows:

* KPI reporting
* customer behavior analysis
* business reasoning
* SQL beyond basic filtering

***

# 🏆 4) Rank Customer Segments by Revenue Lost to Churn

Now let’s prioritize where churn hurts the business most.

```sql
WITH churned_revenue AS (
    SELECT
        c.segment,
        DATE_TRUNC('month', s.end_date) AS churn_month,
        SUM(s.monthly_revenue) AS lost_mrr
    FROM subscriptions s
    JOIN customers c
        ON s.customer_id = c.customer_id
    WHERE s.end_date IS NOT NULL
    GROUP BY c.segment, DATE_TRUNC('month', s.end_date)
),

segment_ranked AS (
    SELECT
        segment,
        churn_month,
        lost_mrr,
        RANK() OVER (
            PARTITION BY churn_month
            ORDER BY lost_mrr DESC
        ) AS revenue_loss_rank
    FROM churned_revenue
)

SELECT *
FROM segment_ranked
ORDER BY churn_month, revenue_loss_rank;
```

***

# 🧠 Explanation

This query introduces a **window function**:

```sql
RANK() OVER (PARTITION BY churn_month ORDER BY lost_mrr DESC)
```

That lets you rank segments by lost revenue each month.

This is useful for answering:

* Which customer segment caused the biggest revenue loss?
* Which segment needs retention attention first?
* Where should customer success focus?

***

# 💡 Key SQL Concepts Demonstrated

This case demonstrates several advanced SQL concepts that are highly relevant in real jobs.

## 1. CTEs

Used to break complex logic into modular steps.

```sql
WITH step_1 AS (...),
step_2 AS (...),
step_3 AS (...)
SELECT * FROM step_3;
```

Why it matters:

* improves readability
* easier to debug
* mimics data pipeline logic

***

## 2. Conditional Aggregation

Used to turn row-level data into KPIs.

```sql
COUNT(CASE WHEN condition THEN 1 END)
SUM(CASE WHEN condition THEN revenue ELSE 0 END)
```

Why it matters:

* powers business dashboards
* common in KPI reporting

***

## 3. Window Functions

Used for ranking and advanced analytics without collapsing rows.

Examples:

* `RANK()`
* `ROW_NUMBER()`
* `SUM() OVER()`
* `AVG() OVER()`

Why it matters:

* essential for analytical SQL
* frequently used in interviews

***

## 4. Date-Based Logic

Many real-world KPI problems depend on time.

Examples:

* monthly churn
* active subscription windows
* usage before cancellation
* cohort analysis

Why it matters:

* business metrics are almost always time-based

***

## 5. Multi-Step KPI Modeling

This case shows how to move from:

* raw subscriptions  
  to
* month-level activity flags  
  to
* aggregated KPI outputs
***









