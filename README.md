# SQL Portfolio Project - Data Analysis

## Table of Contents
- [Project Overview](#project-overview)
- [Ask](#ask)
- [Prepare](#prepare)
- [Process](#process)
- [Analyze](#analyze)
- [Results & Insights](#results--insights)
- [Conclusion](#conclusion)

## Project Overview
This project demonstrates my SQL skills by analyzing job postings data to uncover key insights and trends. The project follows the Ask, Prepare, Process, Analyze methodology to ensure a structured approach to data analysis.

## Ask

### Business Problem:
- What are the highest-paying Data Analyst roles in the UK?
- How do salaries vary across locations and companies?
- What skills are most in demand for Data Analyst roles in the UK?
- What are the most optimal skills based on both demand and salary?

## Prepare

### Dataset Information:
- **Source:** Job postings database
- **Format:** SQL database
- **Description:** Contains job postings with company details, salary information, job locations, and schedules.

### Data Cleaning Considerations:
- Filtering for relevant job titles
- Removing records with missing salary data
- Standardizing job location formats

## Process

### SQL Queries Used:

**Query 1: Top 10 Highest-Paying Data Analyst Jobs**

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location LIKE '%UK%' AND 
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC    
LIMIT 10;

```

```sql
WITH top_paying_jobs AS(
SELECT
    job_id,
    job_title,
    salary_year_avg,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location LIKE '%UK%' AND 
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC    
LIMIT 10 
)
```

```sql
SELECT top_paying_jobs.*, skills
FROM top_paying_jobs
INNER JOIN skills_job_dim
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;

SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_location LIKE '%UK%'   
GROUP BY skills
ORDER BY demand_count DESC    
LIMIT 5;
```

```sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst' 
      AND job_location LIKE '%UK%'   
    GROUP BY skills_dim.skill_id
), 
average_salary AS (
    SELECT 
        skills_dim.skill_id,
        ROUND(AVG(salary_year_avg),2) AS salary_year_avg
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst' 
      AND job_location LIKE '%UK%' 
      AND salary_year_avg IS NOT NULL
    GROUP BY skills_dim.skill_id
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    skills_demand.demand_count,
    average_salary.salary_year_avg
FROM skills_demand
INNER JOIN average_salary
    ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 100    
ORDER BY  salary_year_avg DESC, demand_count DESC
LIMIT 25;
```

## Database Management

### Tools Used:
- **SQL**: Structured Query Language (SQL) was used to interact with and manipulate the database in this project. SQL allows for querying, cleaning, and performing data analysis to uncover insights from the job postings data.

### Table Structures:
- **job_postings_fact**: This table stores job posting data, including job ID, title, location, salary, company ID, and posting date. It serves as the central table in the analysis.
  
- **company_dim**: This table contains information about the companies posting the job advertisements, including company ID, name, industry, and company size.
  
- **skills_job_dim**: This table links jobs to the required skills for those roles, allowing for a many-to-many relationship between jobs and skills.
  
- **skills_dim**: This table stores the details of each skill, including skill ID and skill name, and is linked to the `skills_job_dim` table to identify the skills required for each job.

## Analyze

### Key Findings:
- The highest-paying Data Analyst-related role is Research Engineer, Science at DeepMind in London, with an annual salary of £177,283.
- Data Architect roles appear frequently among the top-paying positions, with salaries around £165,000.
- The most common skills required for high-paying roles include Python, SQL, C++, Pandas, TensorFlow, and AWS.
- The top five most in-demand skills for Data Analysts in the UK are SQL, Excel, Power BI, Python, and Tableau.
- The most optimal skills based on both demand and salary include AWS, Oracle, SQL Server, Azure, PowerPoint, GDPR, Power BI, and Python.

## Conclusion

This project showcases my ability to perform SQL-based data analysis. By following the structured approach, I was able to extract meaningful insights that could support job seekers in identifying high-paying opportunities in the UK Data Analyst job market.

### Next Steps:
- Further optimize queries for efficiency
- Integrate data visualization tools
- Explore additional datasets for deeper analysis

## Contact

For any questions or collaborations, feel free to connect with me on [LinkedIn/GitHub].