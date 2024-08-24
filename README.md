# Introduction
Dive into the data job market. Focusing on data scientist roles, this project explores top-paying jobs, in-demand skills, and where high demand meets high salary in data analytics.

Here goes the SQL queries: [project_sql folder](/project_sql/)
# Background
In order to navigate the data scientist job market more effectively, this project has the objective to share the top-paid and in-demand skills. As well as practicing my SQL skills.

The data was taken from [Luke Barousse - SQL for Data Analytics Course](https://lukebarousse.com/sql)

### The questions I wanted to answer through SQL queries were the following:
1. What are the top-paying data scientist jobs?
2. What are the skills required for these top-paying jobs?
3. What are the most in-demand skills for data scientists?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used
For my deep dive into the data scientist job market, I harnessed the power of several key tools:

- **SQL**: The backbone of the analysis, allowing me to query the database and get critical insights.
- **PostgreSQL**: The chosen database management system (DBMS)
- **Visual Studio Code**: Used for executing the SQL queries
- **Python**: With the use of pandas and plotly libraries, in order to create the charts for data visualization.
- **Git & GitHub**: Essential for version control and sharing the SQL scripts and analysis, ensuring the collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data scientist job market.
Here goes how each question were approached:

### 1. Top Paying Data Scientist Jobs
To identify the highest-paying roles, I filtered data scientist positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT 
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Scientist' AND
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10
```
#### Insights:

- **Elite compensation:** All roles offer salaries of $300,000 or higher, demonstrating the high value placed on top-tier data science talent.
- **Wide range at the top:** There's a significant gap of $250,000 between the highest ($550,000) and lowest ($300,000) salaries, indicating substantial variation even among top positions.
- **Clustering with outliers:** While 70% of the roles cluster between $300,000-$325,000, two standout positions command salaries over $500,000, suggesting premium pay for specialized skills or responsibilities.

![Top Paying Roles](/assets/top_paying_job.png)
*Bar graph visualizing the salary for the top 10 salaries for data scientists;*



### 2. Top Paying Job Skills for Data Scientists

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        job_posted_date,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Scientist' AND
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

#### Insights:

- **Programming skills dominate:** Python and SQL are the most sought-after skills, appearing in half or more of the top roles.
- **Diverse tech stack:** High-paying roles often require knowledge across multiple domains, including big data technologies (Spark, Hadoop, and Cassandra), cloud platforms (GCP, AWS & Azure), and machine learning frameworks.
- **Specialization matters:** While core programming skills are crucial, specialized knowledge in areas like data visualization, specific ML frameworks such as: TensorFlow and PyTorch, or cloud platforms can set candidates apart for top-tier positions.

![Top Paying Skills](/assets/top_paying_skills.png)
*Bar graph visualizing the salary for the top 10 salaries for data scientists;*


### 3. In-Demand Skills for Data Scientists

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Scientist' AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```

| Skills   | Demand Count |
|----------|--------------|
| python   | 10390        |
| sql      | 7488         |
| r        | 4674         |
| aws      | 2593         |
| tableau  | 2458         |

#### Insights:

- **Programming dominance:** Python, SQL, and R are the top three most demanded skills, highlighting the crucial importance of coding and data manipulation abilities in data science roles.
- **Cloud and visualization emerging:**AWS and Tableau's presence in the top 5 reflects the growing significance of cloud computing and data visualization skills in the field.
- **Skill gap:** There's a substantial difference in demand between the top three skills and the others, suggesting that proficiency in Python, SQL, or R might be considered essential for many data science positions.




### 4. Skills Based on Salary

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 2) AS avg_salary
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
LIMIT 25
```
| Skills        | Average Salary ($) |
|---------------|-------------------|
| gdpr          | 217737.50          |
| golang        | 208750.00          |
| atlassian     | 189700.00          |
| selenium      | 180000.00          |
| opencv        | 172500.00          |
| neo4j         | 171655.00          |
| microstrategy | 171146.58          |
| dynamodb      | 169670.33          |
| php           | 168125.00          |
| tidyverse     | 165512.50          |
| solidity      | 165000.00          |
| c             | 164864.98          |
| go            | 164691.09          |
| datarobot     | 164500.00          |
| qlik          | 164484.64          |
| redis         | 162500.00          |
| watson        | 161710.00          |
| rust          | 161250.00          |
| elixir        | 161250.00          |
| cassandra     | 160850.00          |
| looker        | 158714.91          |
| slack         | 158333.33          |
| terminal      | 157500.00          |
| airflow       | 157414.13          |
| julia         | 157243.80          |


#### Insights:
- **Diverse skill set:** The top 5 skills span data privacy (GDPR), programming (Golang), collaboration tools (Atlassian), testing (Selenium), and computer vision (OpenCV), indicating demand for multifaceted data scientists.
- **Data privacy prioritized:** GDPR knowledge commands the highest average salary, highlighting the critical importance of data protection expertise in data science roles.
- **Technical breadth valued:** High salaries for skills in modern programming (Golang), automation (Selenium), and specialized areas (OpenCV) suggest employers value data scientists with a broad technical toolkit.

### 5. Most Optimal Skills to Learn as Data Scientist

```sql
WITH skills_demand AS(
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Scientist' 
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT 
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg), 2) AS avg_salary
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
FROM
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE
    demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 15
```
| Skill ID | Skills     | Demand Count | Average Salary ($) |
|----------|------------|--------------|--------------------|
| 26       | c          | 48           | 164864.98           |
| 8        | go         | 57           | 164691.09           |
| 187      | qlik       | 15           | 164484.64           |
| 185      | looker     | 57           | 158714.91           |
| 96       | airflow    | 23           | 157414.13           |
| 77       | bigquery   | 36           | 157142.36           |
| 3        | scala      | 56           | 156701.92           |
| 81       | gcp        | 59           | 155810.57           |
| 80       | snowflake  | 72           | 152686.88           |
| 101      | pytorch    | 115          | 152602.70           |
| 78       | redshift   | 36           | 151708.14           |
| 99       | tensorflow | 126          | 151536.49           |
| 233      | jira       | 22           | 151164.55           |
| 92       | spark      | 149          | 150188.49           |
| 76       | aws        | 217          | 149629.96           |

#### Insights:
**Salary Range:** The average salary for the skills listed in the file ranges from approximately $143,828 to $164,865. The mean salary across all skills is about $152,080.

- **Highest Paying Skill:** The skill with the highest average salary is C, with an average earning of $164,865. This indicates a strong demand for expertise in this programming language, leading to higher compensation.

- **Lowest Paying Skill:** Surprisingly, Python, one of the most widely used programming languages in data science, has the lowest average salary at $143,828. Despite its popularity, the market saturation could be a reason for the relatively lower compensation.

# What I Learned?
Throughout this project, I have increased my knowledge on SQL.
- **Advanced Queries:** Learn the ability to merge tables and manipulate *WITH* clauses for temporary table maneuvers.
- **Data Aggregation:** Got more acquainted with *GROUP BY* and turned aggregate functions like *COUNT()* and *AVG()* into my data-summarizing arsenal.
- **Analytical Acumen:** Increased my real-world puzzle-solving skills, transforming questions into actionable, insightful SQL queries.

