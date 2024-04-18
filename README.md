# SQL Project
## Introduction
To gather data analysis portfolio, these SQL projects can prove that I gained better understanding in SQL before I attend this SQL course.

Check SQL queries project here: [sql_luke_project](/sql_project/sql_luke_project/)

## Background
Drive a quest to navigate the data scientist job market more effectively, this project was born from a desire pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

### The problem statement across my SQL queries:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

## Tools I Used
For my deep dive into data scientist job market, I leveraged several key tools:
- **SQL**: The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL**: The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code**: My go-to for database management and executing SQL queries.
- **Git & GitHub**: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

## The Analysis
Each query for this project aimed at investigating specific aspects of the data scientist job market. Here's how I approached each question:
### 1. Top Paying Data Scientist Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Scientist' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
```
Here's the breakdown of the top data scientist jobs in 2023:

- Wide Salary Range: Top 10 paying data scientist roles span from $300,000 to $550,000, indicating significant salary potential in the field.
- Diverse Employers: Companies like Selby Jennings, Algo Capital Group, and Demandbase are among those offering high salaries, showing a broad interest across different industries.
- Job Title Variety: There's a high diversity in job titles, from Data Data Scientist to Principal Data Scientist, reflecting varied roles and specializations within data analytics.
### 2. Skills for the Top-paying Data Scientist Jobs
To find what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```
WITH top_paying_jobs AS(
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Scientist' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```
After launched query above, the most demand skills for the top 10 highest paying data scientist jobs in 2023: SQL, Python, Java, Cassandra, Spark, Hadoop, Tableau, Azure, AWS, and Tensorflow.

### 3. The Most In-Demand Skills for Data Scientist
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.
```
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Scientist' AND
    job_location = 'Thailand'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```
Here's the breakdown of the most demanded skills for data analysts in 2023

- Programming and Statistics Tools like Python, R, and SAS are essential, pointing towards the increasing importance of technical skills in data storytelling and decision support.
- SQL is emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.

| Skills | Demand Count |
| ------ | ------------ |
| Python |      47      |
| SQL    |      46      |
| R      |      28      |
| SAS    |      18      |
| AWS    |      15      |

*Table of the most optimal skills for data scientist sorted by salary*

### 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the highest paying.
```
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Scientist'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```
Here's a breakdown of the results for top paying skills for Data Scientist:
- High Demand for Big Data & ML Skills: GDPR (General Data Protection Regulation): this kind of skills is essential and high salary for data scientist that this job need to know how to collect data and processing of personal information from individuals.
- Computing Language: Golang, PHP, C are another computing language which apart from Python. These languages are becoming more popularity and alternative performance language.
- Cloud Computing Expertise: this kind of expertise is data center (Atlassian, Selenium, Cassandra).

### 5. Most Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have salaries, offering a strategic focus for skill development.
```
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count,
        ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_sala
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Scientist'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Scientist'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY
        skills_job_dim.skill_id
)

SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
ORDER BY 
    demand_count DESC,
    avg_salary DESC
LIMIT 10
```
| Skill_id | skills | demand_count | avg_salary |
| -------- | ------ | ------------ | ---------- |
|    1     | Python |      763     |   143828   |
|    0     |  SQL   |      591     |   142833   |
|    5     |   R    |      394     |   137885   |
|   182    | Tableau |     219     |   146970   |
|    76    |  AWS   |     217      |   149630   |
|   92     |  Spark |     149      |   150188   |
|   99     |  Tensorflow | 126     |   151536   |
|   74     | Azure  |     122      |   142306   |
|   101    | Pytorch |    115      |   152603   |
|    93    | Pandas |     113      |   144816   |

*Table of the most optimal skills for data scientist sorted by salary*

Here's a breakdown of the most optimal skills for Data Scientist in 2023:
- High Demand Programming Languages: Python and SQL stand out for their high demand, with demand counts of 763 and 591 respectively. Despite their high demand, their average salaries are around $143,828 for Python and $142,833 for SQL, indicating that proficiency in these languages is highly valued but also widely available.
- Business Intelligence and Visualization Tools: Tableau, with demand counts of 219 respectively, and average salaries around $146,970, highlight the critical role of data visualization and business intelligence in deriving actionable insights from data.
- Cloud Tools and Technologies: Skills in specialized technologies such as Azure, AWS, and Spark show significant demand with relatively high average salaries, pointing towards the growing importance of cloud platforms and big data technologies in data analysis.

## What I Learned
Throughout this project, I've leveraged my SQL toolkit:
- Complex Query: Advanced SQL, merging tables (JOIN) and wielding WITH clauses
- Data Aggregation: Apply with GROUP BY and turned aggregate function like SUM(), COUNT(), and AVG() into data-summarizing.
- Analytical Wizardry: Leveled up real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

## Conclusions
### Insights

Revised problem-solving from SQL with insightful emerged:
1. **Top-Paying Data Scientist Jobs**: The highest-paying jobs for data scientists that allow remote work offer a wide range of salaries, the highest at $550,000.
2. **Skills for Top-Paying Jobs**: High-paying data scientist jobs require advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary.
3. **Most In-Demand Skills**: Python is the most demanded skill for data scientist job market which can attract for job seekers.
4. **Skills with Higher Salaries**: Specialized skills, such as GDPR, Atlassian, Golang, and other skills, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: Python leads in demand, following by SQL are the most optimal skills for data scientist to learn to maximize their market value.

## Closing Thoughts
This project developed my SQL skills from zero to hero and provided valuable insights into data scientist job market. The findings from the analysis serve as a guide to prioritize skill development and job search efforts. This exploration highlights the importance of continuous learnign and adaptation to emerge trends in the field of data analytics.