# Introduction
My first SQL project is inspired by [Luke Barousse](https://github.com/lukebarousse)—be sure to check out his other amazing content on [YouTube](https://www.youtube.com/@LukeBarousse) or [LinkedIn](https://www.linkedin.com/in/luke-b/)!

In this project, I dive into the data job market, with a specific focus on data analyst roles. Through this analysis, I explore:

Top-paying jobs
In-demand skills
Regions where high demand aligns with high salaries in the data analytics field.


# Background

The questions I wanted to answer through my SQL queries were:
What are the top-paying data analyst jobs?
What skills are required for these top-paying jobs?
What skills are most in demand for data analysts?
Which skills are associated with higher salaries?
What are the most optimal skills to learn?

# Tools I Used

I conducted an in-depth analysis of the data analyst job market, utilizing a variety of essential tools:

**SQL:** The foundation of my analysis, enabling me to query databases and extract key insights.
**PostgreSQL:** The database management system of choice, ideal for managing and analyzing job posting data.
**Visual Studio Code:** My primary platform for managing the database and running SQL queries.
**Git & GitHub:** Crucial for version control and sharing my SQL scripts and findings, promoting collaboration and tracking project progress.
**ChatGPT:** Assisted me with debugging and resolving coding issues throughout the project.

# The Analysis

### 1_top_paying_jobs

```sql
select
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as comapny_name
from    
    job_postings_fact 
left join company_dim on job_postings_fact.company_id = company_dim.company_id 
-- left join function is to find out the company name and add the column
where 
    job_title_short = 'Data Analyst' and 
    job_location = 'Anywhere' and 
    salary_year_avg is not null
order by
    salary_year_avg desc
limit 10;
```

Here’s the breakdown of the top data analyst jobs in 2023:

**Wide Salary Range:** The top 10 highest-paying data analyst roles offer salaries ranging from $184,000 to $650,000, highlighting substantial earning potential in the field.
**Diverse Employers:** Major companies like SmartAsset, Meta, and AT&T are offering high-paying positions, showcasing demand across various industries.
**Job Title Variety:** There's a broad range of job titles, from Data Analyst to Director of Analytics, illustrating the diverse roles and specializations within the data analytics domain.

![Top Paying Roles]("C:\Users\felix\SQL_Project_Data_Job_Analysis\1_top_paying_job.png")
*ChatGPT generated this graph from my SQL query results*

bar chart visualizing the top-paying data analyst roles in 2023, with salaries ranging from $184,000 to $650,000. The highest salary is for a Data Analyst role at Mantys, and the lowest for an ERM Data Analyst at Get It Recruit - Information Technology. The chart also highlights other high-paying roles across companies like Meta, AT&T, and SmartAsset

### 2_top_paying_job_skills

To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
with top_paying_jobs as (
    select
        job_id,
        job_title,
        salary_year_avg,
        name as comapny_name
    from    
        job_postings_fact 
    left join company_dim on job_postings_fact.company_id = company_dim.company_id 
    -- left join function is to find out the company name and add the column
    where 
        job_title_short = 'Data Analyst' and 
        job_location = 'Anywhere' and 
        salary_year_avg is not null
    order by
        salary_year_avg desc
    limit 10
)

select 
    top_paying_jobs.*,
    skills
from top_paying_jobs
inner join skills_job_dim on top_paying_jobs.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
-- second inner join is to give the name of skills from skill ID
order by    
    salary_year_avg desc;
```

Here’s the breakdown of the most in-demand skills for the top 10 highest-paying data analyst jobs in 2023:

**SQL** leads the pack, being required in 8 of the top roles.
**Python** is a close second, appearing in 7 of the job listings.
**Tableau** is also highly sought after, showing up in 6 of the roles.
**Other skills** like R, Snowflake, Pandas, and Excel are also in demand but with varying frequencies.

![Top Paying Skills]("C:\Users\felix\SQL_Project_Data_Job_Analysis\2_top_paying_roles_skills.png")

*ChatGPT generated this graph from my SQL query results*

### 3_top_demanded_skills

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
select
  skills,
  count(skills_job_dim.job_id) as demand_count
from job_postings_fact
inner join skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
where
  job_title_short = 'Data Analyst' and 
  job_work_from_home = true
group by 
  skills
order by
  demand_count desc
limit 5
;
```

Most In-Demand Skills for Data Analysts in 2023:

**SQL** and **Excel** are still key skills, highlighting the importance of strong basics in handling data and using spreadsheets.
Skills in Programming and Visualization Tools like **Python**, **Tableau**, and **Power BI** are becoming increasingly important, showing the need for technical skills in presenting data and supporting decision-making.

| Skills   | Demand Count |
|----------|--------------|
| SQL      | 7291         |
| Excel    | 4611         |
| Python   | 4330         |
| Tableau  | 3745         |
| Power BI | 2609         |

*Table of the demand for the top 5 skills in data analyst job postings*

### 4_top_paying_skills

Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
select
  skills,
  ROUND(avg(salary_year_avg),2) as avg_salary
from job_postings_fact
inner join skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
where
    job_title_short = 'Data Analyst' 
    and salary_year_avg IS NOT null 
    and job_work_from_home = true
group by 
    skills
order by 
    avg_salary DESC
limit 30;
```

Here’s an overview of the top-paying skills for Data Analysts:

**High Demand for Big Data & Machine Learning Skills:** Analysts with expertise in big data technologies (like PySpark and Couchbase), machine learning tools (such as DataRobot and Jupyter), and Python libraries (like Pandas and NumPy) earn the highest salaries. This shows that the industry values skills in data processing and predictive modeling highly.

**Software Development & Deployment Skills:** Knowledge of development and deployment tools (like GitLab, Kubernetes, and Airflow) is valuable. This skill set highlights a rewarding link between data analysis and engineering, especially for those who can automate processes and manage data pipelines effectively.

**Cloud Computing Knowledge:** Understanding cloud and data engineering tools (like Elasticsearch, Databricks, and Google Cloud Platform) is increasingly important. This expertise can significantly enhance earning potential in data analytics, reflecting the rising trend of cloud-based analytics.

| Skills        | Average Salary ($) |
|---------------|-------------------:|
| pyspark       |            208,172 |
| bitbucket     |            189,155 |
| couchbase     |            160,515 |
| watson        |            160,515 |
| datarobot     |            155,486 |
| gitlab        |            154,500 |
| swift         |            153,750 |
| jupyter       |            152,777 |
| pandas        |            151,821 |
| elasticsearch |            145,000 |

*Table of the average salary for the top 10 paying skills for data analysts*

### 5_optimal_skills_to_learn

This analysis combined data on skill demand and salaries to identify skills that are both sought after and well-paying, helping to guide future skill development efforts.

```sql

-- Identifies skills in high demand for Data Analyst roles
-- Use Query #3

with skills_demand as (
select
  skills_dim.skill_id,
  skills_dim.skills,
  count(skills_job_dim.job_id) as demand_count
from job_postings_fact
inner join skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
where
  job_title_short = 'Data Analyst'  
  and salary_year_avg IS NOT null 
  and job_work_from_home = true
group by 
  skills_dim.skill_id
),

average_salary as (
select
  skills_job_dim.skill_id,
  ROUND(avg(salary_year_avg),2) as avg_salary
from job_postings_fact
inner join skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
where
    job_title_short = 'Data Analyst' 
    and salary_year_avg IS NOT null 
    and job_work_from_home = true
group by 
    skills_job_dim.skill_id
)

select
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
from
    skills_demand
inner join average_salary on skills_demand.skill_id = average_salary.skill_id
where
    demand_count >10
order by
    avg_salary desc,
    demand_count desc
limit 25;
```

| Skill ID | Skills     | Demand Count | Average Salary ($) |
|----------|------------|--------------|-------------------:|
| 8        | go         | 27           |            115,320 |
| 234      | confluence | 11           |            114,210 |
| 97       | hadoop     | 22           |            113,193 |
| 80       | snowflake  | 37           |            112,948 |
| 74       | azure      | 34           |            111,225 |
| 77       | bigquery   | 13           |            109,654 |
| 76       | aws        | 32           |            108,317 |
| 4        | java       | 17           |            106,906 |
| 194      | ssis       | 12           |            106,683 |
| 233      | jira       | 20           |            104,918 |

*Table of the most optimal skills for data analyst sorted by salary*

Key Skills for Data Analysts in 2023:
**Popular Programming Languages:** **Python** and **R** are highly sought after, with demand counts of 236 and 148, respectively. Their average salaries are about $101,397 for **Python** and $100,499 for **R**, showing that while these languages are in demand, there are many analysts who know them.

**Cloud Technologies:** Skills in tools like **Snowflake**, **Azure**, **AWS**, and **BigQuery** are in high demand and come with strong average salaries. This emphasizes the increasing importance of cloud and big data technologies in data analysis.

**Data Visualization and BI Tools:** **Tableau** and **Looker** are important for visualizing data, with demand counts of 230 and 49, and average salaries around $99,288 and $103,795. This highlights how crucial these tools are for turning data into useful insights.

**Database Skills:** Skills in both traditional and **NoSQL** databases, such as **Oracle** and **SQL** Server, are still highly needed, with salaries ranging from $97,786 to $104,534. This indicates the ongoing demand for expertise in data storage and management.


# What I Learned

Completed a 4-hour course learning how to write basic SQL queries and tackling more advanced ones, with a focus on improving my SQL skills.
Developed analytical thinking by gathering, cleaning, and analyzing data, solving real-world problems, and transforming questions into insightful information.
Gained proficiency in using Visual Studio Code for SQL queries and GitHub for uploading and sharing my projects.


# Conclusion

## Insight

**High Earning Potential:** The data analyst field offers substantial salary ranges, with top positions earning between $184,000 and $650,000. This indicates a lucrative career path for those with the right skills and experience.

**Essential Skills in Demand:** SQL and Excel continue to be foundational skills that employers seek. Additionally, programming languages like Python and visualization tools such as Tableau are becoming increasingly important, highlighting a shift towards more technical roles in data analytics.

**Focus on Emerging Technologies:** The analysis highlighted the growing demand for expertise in big data technologies, machine learning, and cloud computing. Skills in tools like Snowflake, AWS, and Python libraries are highly valued, reflecting industry trends towards automation and advanced data analysis.

**Diverse Career Opportunities:** The wide variety of job titles within the data analyst domain—from Data Analyst to Director of Analytics—demonstrates the many pathways available. This diversity allows professionals to find roles that best align with their interests and skill sets.

**Strategic Skill Development:** The findings emphasize the importance of continuous learning and skill enhancement in areas that not only meet current market demand but also command higher salaries. By focusing on high-demand skills such as cloud technologies and programming, aspiring data analysts can significantly boost their earning potential and career prospects.

## Conclusion Thoughts
This SQL project has been a valuable and enlightening journey into the data analyst job market. By leveraging SQL queries and data analysis techniques, I was able to uncover critical insights about job roles, skills in demand, and salary trends within the industry. This project not only deepened my understanding of the skills required to thrive as a data analyst but also highlighted the evolving nature of the field amidst technological advancements.