# Project Overview

This project explores the data job market using SQL-based analysis. The main focus is understanding salary trends, identifying in-demand skills, and finding which technical skills provide the best career opportunities in tech roles.

All SQL queries used for the analysis are located in the `project_sql` folder.

---

# Project Background

The idea behind this project was to use SQL to analyze job posting data and uncover useful insights. Instead of manually browsing job listings, the queries help identify patterns related to compensation, hiring demand, and technical skill requirements.

The dataset includes information such as:

* Job titles
* Salary estimates
* Company names
* Job locations
* Required technical skills

---

# Business Questions

The analysis focuses on answering the following questions:

1. What are the top-paying remote jobs?
2. What skills are required for the top-paying jobs?
3. What skills are most in demand?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

---

# Technologies Used

* **SQL** — Used to write queries and analyze the dataset
* **PostgreSQL** — Used as the relational database management system
* **Visual Studio Code** — Used for writing and running SQL scripts
* **Git & GitHub** — Used for version control and project tracking

---

# Project Structure

```text
.
├── 1_top_paying_jobs.sql
├── 2_top_paying_job_skills.sql
├── 3_top_demanded_skills.sql
├── 4_top_paying_skills.sql
└── 5_optimal_skills.sql
```

---

# SQL Analysis

Each SQL file investigates a different part of the data job market and helps answer a specific business question.

## 1. Top Paying Remote Jobs

**File:** `1_top_paying_jobs.sql`

This query retrieves the highest-paying remote positions by filtering records based on:

* Remote location (`job_location = 'Anywhere'`)
* Non-null salary information

### SQL Concepts Used

* `LEFT JOIN`
* `WHERE`
* `ORDER BY`
* `LIMIT`

```sql
SELECT 
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10
```

---

## 2. Skills Required for Top Paying Jobs

**File:** `2_top_paying_job_skills.sql`

This query identifies the technical skills connected to the highest-paying jobs.

A Common Table Expression (CTE) is used to first isolate the highest-paying jobs, followed by joins to retrieve the related skills.

### SQL Concepts Used

* `WITH` clause (CTE)
* `INNER JOIN`
* `LEFT JOIN`
* Multi-table joins

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)
SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```

---

## 3. Top Demanded Skills

**File:** `3_top_demanded_skills.sql`

This query measures skill demand by counting how often each skill appears across job postings.

### SQL Concepts Used

* `COUNT()`
* `GROUP BY`
* Aggregation
* `ORDER BY`
* Ranking

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 10
```

---

## 4. Top Paying Skills

**File:** `4_top_paying_skills.sql`

This query calculates the average salary linked to each skill to identify the most valuable technical skills.

### SQL Concepts Used

* `AVG()`
* `ROUND()`
* Aggregation functions
* Salary analysis

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 20
```

---

## 5. Optimal Skills to Learn

**File:** `5_optimal_skills.sql`

This query combines salary data with demand data to determine which skills provide the strongest market value.

The query filters skills that:

* Appear in more than 10 jobs
* Have strong average salary potential
* Maintain high market demand

### SQL Concepts Used

* `HAVING`
* `COUNT()`
* `AVG()`
* Multi-condition sorting
* Aggregation

```sql
SELECT skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    salary_year_avg IS NOT NULL
GROUP BY skills_dim.skill_id,
    skills_dim.skills
HAVING COUNT(skills_job_dim.job_id) > 10
ORDER BY avg_salary DESC,
    demand_count DESC
LIMIT 20;
```

---

# What I Learned

Working on this project helped improve both my SQL knowledge and analytical thinking skills:

* **Advanced SQL Queries** — Improved understanding of joins, filtering, aggregation, and sorting
* **CTEs (Common Table Expressions)** — Learned how to break complex queries into more readable sections
* **Data Aggregation** — Used functions like `COUNT()` and `AVG()` to summarize large datasets
* **Analytical Thinking** — Translated business questions into practical SQL solutions
* **Database Relationships** — Worked with multiple related tables to generate insights

---

# Final Thoughts

This project demonstrates how SQL can be used to explore real-world job market data and uncover meaningful insights.

Some of the main observations from the analysis include:

* Remote data jobs can offer highly competitive salaries
* Technical skills play a major role in determining salary potential
* Certain skills consistently appear across high-paying and high-demand jobs
* Combining salary and demand analysis helps identify valuable skills to learn

Overall, the project helped strengthen practical SQL skills while providing a better understanding of industry trends and technical skill demand.

---

# Future Improvements

Potential enhancements for this project include:

* Data visualizations and dashboards
* Trend analysis over time
* Location-based comparisons
* Role-specific analysis
* Experience-level filtering
* Industry segmentation

---

# License

This project is open-source and intended for educational and portfolio purposes.
