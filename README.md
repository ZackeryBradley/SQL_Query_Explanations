# 🧠 SQL Query Explanations & Practice

This repository demonstrates SQL proficiency across three levels:

- **Basic SQL** → foundational querying, filtering, aggregation, and date logic  
- **Intermediate SQL** → joins, subqueries, ranking, and window functions  
- **Advanced SQL** → KPI modeling, customer churn analysis, multi-step transformations, and business-facing SQL  

The goal of this project is to show how SQL is used in real-world data roles to:

- answer business questions  
- transform raw data into insights  
- build reusable KPI logic  
- support dashboards, reporting, and analytics workflows  

---

## 🛠️ Skills Demonstrated

- Data querying and filtering
- Aggregations and grouping
- Joins and relational modeling
- Subqueries
- Window functions
- Common Table Expressions (CTEs)
- KPI and metric development
- Time-based analytics
- Revenue and churn analysis
- Business logic in SQL

---

## 📈 Progression of Complexity

| Level        | Focus Area                                      |
|--------------|--------------------------------------------------|
| Basic        | Filtering, aggregation, and date conditions      |
| Intermediate | Joins, subqueries, ranking, window functions     |
| Advanced     | KPI modeling, churn analysis, MRR, transformations |

---

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

| id | name    | department | salary | hire_date  |
|----|---------|------------|--------|------------|
| 1  | Alice   | HR         | 60000  | 2021-06-15 |
| 2  | Bob     | IT         | 80000  | 2020-03-10 |
| 3  | Charlie | IT         | 75000  | 2022-01-05 |
| 4  | Diana   | Finance    | 90000  | 2019-11-20 |
| 5  | Evan    | HR         | 65000  | 2023-07-01 |

---

## ❓ Questions

Write SQL queries to answer the following:

1. Retrieve all columns for employees in the **IT department**
2. Get the **names and salaries** of employees earning more than **70,000**
3. Count the number of employees in each department
4. Find the **average salary** of all employees
5. Retrieve employees hired **after January 1, 2021**

---

## ✅ Solutions

### 1. Employees in the IT department

```sql
SELECT *
FROM employees
WHERE department = 'IT';
```

---

### 2. Names and salaries of employees earning more than 70,000

```sql
SELECT name, salary
FROM employees
WHERE salary > 70000;
```

---

### 3. Count of employees by department

```sql
SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

---

### 4. Average salary of all employees

```sql
SELECT AVG(salary) AS avg_salary
FROM employees;
```

---

### 5. Employees hired after January 1, 2021

```sql
SELECT *
FROM employees
WHERE hire_date > '2021-01-01';
```

---

## 🧠 Explanation

### 🔹 SELECT

The `SELECT` statement is used to retrieve data from a table.

- `SELECT *` returns all columns
- `SELECT column1, column2` returns specific columns

---

### 🔹 WHERE

The `WHERE` clause filters rows based on a condition.

- `salary > 70000` filters only high earners
- `department = 'IT'` filters rows by department

---

### 🔹 GROUP BY

The `GROUP BY` clause groups rows that share the same value.

- Often used with aggregate functions like `COUNT()` and `AVG()`
- Common for summary reporting

---

### 🔹 Aggregate Functions

These functions summarize data across multiple rows.

- `COUNT()` → number of rows
- `AVG()` → average value
- `SUM()` → total
- `MIN()` → smallest value
- `MAX()` → largest value

---

### 🔹 Date Filtering

SQL allows filtering by date using comparison operators.

Example:

```sql
WHERE hire_date > '2021-01-01'
```

This returns employees hired after January 1, 2021.

---

## 💼 Business Context

These basic SQL patterns are essential for:

- operational reporting
- filtering business data
- summarizing metrics
- preparing data for dashboards and analysis

---

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
-----------
department_id (INT)
department_name (VARCHAR)
```

### Sample Data

#### employees

| id | name    | department_id | salary | hire_date  |
|----|---------|---------------|--------|------------|
| 1  | Alice   | 1             | 60000  | 2021-06-15 |
| 2  | Bob     | 2             | 80000  | 2020-03-10 |
| 3  | Charlie | 2             | 75000  | 2022-01-05 |
| 4  | Diana   | 3             | 90000  | 2019-11-20 |
| 5  | Evan    | 1             | 65000  | 2023-07-01 |
| 6  | Frank   | 2             | 95000  | 2021-09-18 |

#### departments

| department_id | department_name |
|---------------|-----------------|
| 1             | HR              |
| 2             | IT              |
| 3             | Finance         |

---

## ❓ Questions

Write SQL queries to:

1. Join employees with departments and display employee names with department names
2. Find the **highest-paid employee in each department**
3. Rank employees by salary within each department
4. Calculate the **running total of salaries** by department
5. Find employees who earn **above the company average salary**
6. Get the **second highest salary** in the company

---

## ✅ Solutions

### 1. Employees with department names

```sql
SELECT e.name, d.department_name, e.salary
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id;
```

---

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

---

### 3. Rank employees by salary within each department

```sql
SELECT
    e.name,
    d.department_name,
    e.salary,
    RANK() OVER (
        PARTITION BY e.department_id
        ORDER BY e.salary DESC
    ) AS salary_rank
FROM employees e
JOIN departments d
    ON e.department_id = d.department_id;
```

---

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

---

### 5. Employees earning above average salary

```sql
SELECT name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

---

### 6. Second highest salary in the company

```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (
    SELECT MAX(salary)
    FROM employees
);
```

---

## 🧠 Explanation

### 🔹 JOINs

Joins combine data from multiple tables using a shared key.

- `INNER JOIN` returns matching rows from both tables
- This is essential when working with normalized relational databases

---

### 🔹 Subqueries

A subquery is a query nested inside another query.

Examples:
- comparing salary to the company average
- finding the highest or second-highest value

Subqueries can be:
- **independent**
- **correlated** (dependent on the outer query)

---

### 🔹 Window Functions

Window functions perform calculations across a group of rows without collapsing them into one row.

Common examples:
- `RANK()`
- `DENSE_RANK()`
- `ROW_NUMBER()`
- `SUM() OVER()`

---

### 🔹 PARTITION BY

`PARTITION BY` divides rows into groups for window function calculations.

Example:

```sql
RANK() OVER (PARTITION BY department_id ORDER BY salary DESC)
```

This ranks employees **within each department**.

---

### 🔹 ORDER BY in Window Functions

Inside a window function, `ORDER BY` determines the sequence in which calculations occur.

This is especially important for:
- ranking
- running totals
- moving averages

---

## 💼 Business Context

Intermediate SQL patterns are widely used in:

- departmental reporting
- top-performer analysis
- salary or revenue ranking
- customer or product segmentation
- KPI dashboards

---

# 📊 Advanced SQL Query Practice

## 📌 Business Scenario

You are a Data Analyst at a SaaS company that sells monthly subscriptions.

Leadership wants to understand:

- how many customers are **active**
- how many customers **churned**
- what percentage of customers churned each month
- how much **monthly recurring revenue (MRR)** was lost due to churn
- which customer segments are most at risk

Your task is to write SQL queries that transform raw subscription data into business-facing KPIs.

---

## 🗂️ Database Schema

### 1. `customers`

```sql
customers
---------
customer_id (INT)
customer_name (VARCHAR)
signup_date (DATE)
segment (VARCHAR)
country (VARCHAR)
```

### 2. `subscriptions`

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

### 3. `product_usage`

```sql
product_usage
-------------
usage_id (INT)
customer_id (INT)
usage_date (DATE)
login_count (INT)
feature_uses (INT)
```

---

## 📊 KPI Definitions

- **Active Customer** → customer with an active subscription during a given month  
- **Churned Customer** → customer whose subscription ended during that month  
- **Churn Rate (%)** → churned_customers / active_customers  
- **MRR (Monthly Recurring Revenue)** → sum of recurring subscription revenue  
- **Churned MRR** → revenue lost from canceled subscriptions  

---

## 🎯 Business Questions

Write SQL to answer the following:

1. What is the total number of **active customers** for each month?
2. How many customers **churned each month**?
3. What is the **monthly churn rate**?
4. How much **MRR was lost due to churn** each month?
5. Which customer segment has the **highest churn rate**?
6. Identify customers with **low product usage before churn**
7. Build a final KPI output table for leadership showing:
   - month
   - active_customers
   - churned_customers
   - churn_rate
   - active_mrr
   - churned_mrr

---

## ✅ Advanced SQL Solution

> **Note:** These examples use SQL patterns commonly supported in PostgreSQL, Snowflake, and similar analytic SQL engines.  
> Minor syntax changes may be needed for SQL Server, MySQL, BigQuery, or Redshift.

---

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
    FROM subscriptions s
    JOIN calendar_months cm
        ON s.start_date <= (cm.month_start + INTERVAL '1 month' - INTERVAL '1 day')
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
                ELSE churned_customers * 100.0 / active_customers
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

---

## 🧠 Explanation

This query uses multiple **CTEs** to break a complex business problem into manageable steps.

### 🔹 CTE 1: `calendar_months`

```sql
WITH calendar_months AS (...)
```

This creates the monthly reporting periods.

Why it matters:
- KPI reporting is commonly done at the monthly grain
- calendar logic helps standardize reporting windows

---

### 🔹 CTE 2: `subscription_monthly_status`

```sql
subscription_monthly_status AS (...)
```

This evaluates each subscription against each month and determines:

- whether it was active during that month
- whether it churned during that month

Logic:
- A subscription is **active** if it began before month-end and did not end before the month started
- A subscription is **churned** if the `end_date` falls inside the month

This is a common pattern in analytics:
turning row-level event dates into month-level business flags

---

### 🔹 CTE 3: `monthly_customer_kpis`

This step aggregates row-level flags into monthly metrics:

- active customers
- churned customers
- active MRR
- churned MRR

It uses:
- `COUNT(DISTINCT ...)`
- `SUM(CASE WHEN ...)`

These are foundational techniques for dashboard metric development.

---

### 🔹 CTE 4: `final_kpis`

This calculates the final executive KPI:

```sql
churn_rate_pct = churned_customers / active_customers
```

This is a common example of deriving a business metric from operational data.

---

## 2) Churn Rate by Customer Segment

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

---

## 🧠 Explanation

This query measures churn by customer segment.

Why this matters:
- leaders often need to know which customer groups are most at risk
- retention strategies are commonly targeted by segment
- segment-level churn can reveal where the business is losing the most value

---

## 3) Identify Low-Usage Customers Before Churn

This query identifies customers who churned and had low engagement in the 30 days before cancellation.

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

---

## 🧠 Explanation

This combines churn outcomes with product engagement.

Why this matters:
- shows how behavior data can explain business outcomes
- supports retention analysis
- simulates the type of logic used in churn prediction workflows

This is a strong portfolio example because it demonstrates:
- KPI reporting
- behavioral analysis
- business reasoning
- SQL beyond basic filtering

---

## 4) Rank Customer Segments by Revenue Lost to Churn

This query prioritizes where churn hurts the business most.

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

---

## 🧠 Explanation

This introduces a window function:

```sql
RANK() OVER (PARTITION BY churn_month ORDER BY lost_mrr DESC)
```

This ranks customer segments by lost MRR within each month.

Why this matters:
- helps leadership prioritize retention efforts
- identifies where the greatest revenue damage is occurring
- demonstrates advanced analytical SQL patterns

---

# 💡 Key SQL Concepts Demonstrated

## 1. CTEs

Used to break complex logic into modular steps.

```sql
WITH step_1 AS (...),
step_2 AS (...),
step_3 AS (...)
SELECT * FROM step_3;
```

Why it matters:
- improves readability
- simplifies debugging
- mirrors pipeline-style transformation logic

---

## 2. Conditional Aggregation

Used to turn row-level records into KPI outputs.

```sql
COUNT(CASE WHEN condition THEN 1 END)
SUM(CASE WHEN condition THEN revenue ELSE 0 END)
```

Why it matters:
- supports reporting and dashboards
- commonly used in metric development

---

## 3. Window Functions

Used for advanced calculations without collapsing rows.

Examples:
- `RANK()`
- `ROW_NUMBER()`
- `SUM() OVER()`
- `AVG() OVER()`

Why it matters:
- essential for analytical SQL
- frequently used in data interviews and BI workflows

---

## 4. Date-Based Logic

Many business KPIs depend on time.

Examples:
- monthly churn
- active subscription tracking
- engagement before cancellation
- cohort and retention analysis

Why it matters:
- most business metrics are time-based

---

## 5. Multi-Step KPI Modeling

This case shows how to move from:

- raw subscriptions  
to
- month-level activity flags  
to
- aggregated KPI outputs  

This is very similar to how analytics teams build reusable reporting tables.

---

## ⚠️ Edge Cases & Considerations

In real-world systems, additional complexity often exists.

Examples include:

- customers with multiple active subscriptions
- plan upgrades or downgrades within a month
- prorated billing
- subscription pauses instead of cancellations
- reactivated customers after churn
- late-arriving data
- timezone differences in event timestamps

These scenarios may require:
- more advanced business rules
- event-based modeling
- carefully defined metric logic
- data warehouse transformation layers

---

## ⚡ Performance Considerations

When working with large datasets, performance matters.

Common optimization strategies include:

- avoiding unnecessary Cartesian products or broad `CROSS JOIN`s
- indexing key fields such as `customer_id`, `start_date`, and `end_date`
- partitioning large tables by date
- pre-aggregating monthly KPI tables for dashboard use
- limiting repeated scans over large fact tables

These considerations become especially important in production analytics environments.

---

## 🧱 Logical Data Flow

This project follows a typical analytics workflow:

**Raw tables**
- `customers`
- `subscriptions`
- `product_usage`

⬇️

**Transformation logic**
- monthly reporting periods
- activity/churn flags
- conditional aggregation
- KPI calculations

⬇️

**Final outputs**
- monthly KPI tables
- churn rate by segment
- usage-based churn risk analysis
- ranked revenue loss by segment

---

## 💼 Real-World Applications

These SQL patterns are commonly used in:

- SaaS KPI dashboards
- churn and retention analysis
- product engagement reporting
- financial reporting and MRR tracking
- BI semantic layers
- analytics engineering workflows

Relevant tools and environments include:

- Power BI
- Tableau
- Snowflake
- BigQuery
- Redshift
- PostgreSQL
- dbt

---

## ✅ Summary

This repository shows SQL progression from foundational querying to business-facing advanced analytics.

It demonstrates the ability to:

- write clear SQL across multiple difficulty levels
- solve real-world reporting and KPI problems
- use advanced SQL features such as CTEs and window functions
- connect technical SQL logic to business outcomes

This type of work is directly applicable to roles such as:

- Data Analyst
- Business Intelligence Analyst
- Analytics Engineer
- Data Engineer (analytics-focused)

---
