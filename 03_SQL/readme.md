# SQL Code
This folder contains all SQL scripts used in this project.
```SQL
--1) Total Number of Projects

SELECT
COUNT([name]) AS Total_Projects
FROM [ks-projects-201801]
```
```SQL
--2) Project By State Distribution

SELECT
	state,
	COUNT([name]) Project_per_Stae
FROM [ks-projects-201801]
Group By [state]
Order By COUNT(name) DESC
```
```SQL
--3) Success Rate By Main Category
SELECT
	main_category,
	COUNT(*) Total_Project,
	SUM(CASE WHEN state ='successful' THEN 1 ELSE 0 END) Successful_Project,
	CAST(SUM(CASE WHEN state ='successful' THEN 1 ELSE 0 END)AS FLOAT)/(COUNT(*)) *100 AS Success_rate
FROM [ks-projects-201801]
GROUP BY main_category
ORDER BY Success_rate DESC
```
```SQL
--4) AVERAGE GOAL AND PLEDGED (USD) by Main Category
SELECT
	main_category,
	ROUND(AVG(goal),2) Avg_goal,
	ROUND(AVG(pledged),2) Avg_pledged
FROM [ks-projects-201801]
Group by main_category
Order by ROUND(AVG(goal),2) DESC
```
```SQL
--5) AVERAGE BACKERS BY STATE

SELECT
	state,
	AVG(backers) Avg_Backers
FROM [ks-projects-201801]
GROUP BY state
ORDER BY AVG(backers) DESC
```
```SQL
--6) Projects by Year

SELECT
	YEAR(launched) Year_Launced,
	COUNT(name) Total_Project
FROM [ks-projects-201801]
GROUP BY YEAR(launched)
ORDER BY COUNT(name) DESC
```
```SQL
--7) MONTHLY SUCCESS RATE

SELECT
	MONTH(launched) Launched_Month,
	COUNT(*) Total_Project,
	SUM(CASE WHEN state='successful' THEN 1 ELSE 0 END) Success_Project,
	ROUND(CAST(SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) AS FLOAT)/COUNT(*) *100,2) Success_rate
FROM [ks-projects-201801]
GROUP BY MONTH(launched)
ORDER BY Success_rate DESC
```
```SQL
--8) TOP 10 MOST BACKED PROJECT

SELECT
*
FROM (SELECT
	name,
	backers,
	ROW_NUMBER() OVER(ORDER BY backers DESC) AS R_N
FROM [ks-projects-201801]) tt
WHERE R_n <=10
```
```SQL
--9) TOP 10 HIGHEST FUNDED PROJECT (USD)

SELECT
*
FROM (SELECT
name,
usd_pledged_real,
ROW_NUMBER() OVER(ORDER BY usd_pledged_real DESC) r_n
FROM [ks-projects-201801]) TT
WHERE r_n<=10
```
```SQL
-- 10) HOW PROJECT GOALS AFFECT SUCCESS RATE
SELECT
	CASE
			WHEN usd_goal_real<1000 THEN '<$1K'
			WHEN usd_goal_real BETWEEN 1000 AND 9999 THEN '$1K-$10K'
			WHEN usd_goal_real BETWEEN 10000 AND 49999 THEN '$10K-$50K'
			WHEN usd_goal_real BETWEEN 50000 AND 99999 THEN '$50K-$100K'
			ELSE '<$100000K'
	END AS goal_range,
	COUNT(*) total_project,
	SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) Successful_project,
	ROUND(CAST (SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END)AS FLOAT)/COUNT(*) * 100,2) Success_Rate
FROM [ks-projects-201801]
GROUP BY (CASE
			WHEN usd_goal_real<1000 THEN '<$1K'
			WHEN usd_goal_real BETWEEN 1000 AND 9999 THEN '$1K-$10K'
			WHEN usd_goal_real BETWEEN 10000 AND 49999 THEN '$10K-$50K'
			WHEN usd_goal_real BETWEEN 50000 AND 99999 THEN '$50K-$100K'
			ELSE '<$100000K'
		END)
```
```SQL
--11) Which Categories perform best in each country?
SELECT
	main_category,
	country,
	COUNT(*) Total_Project,
	SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) Suucessful_project,
	ROUND(CAST(SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) AS FLOAT)/COUNT(*) * 100,2) AS Success_rate
FROM [ks-projects-201801]
Group By main_category,country
HAVING COUNT(*) >= 50  -- Needed to remove projects with very little project
ORDER BY Success_rate DESC
```
```SQL
--12) Campaign Duration Impact on Success

ALTER TABLE [dbo].[ks-projects-201801]
ADD Campaign_duration INT

UPDATE [dbo].[ks-projects-201801]
SET Campaign_duration= DATEDIFF(day, launched,deadline)

ALTER TABLE [dbo].[ks-projects-201801]
DROP Campaign_duration

SELECT
	CASE 
		WHEN Campaign_duration <10 THEN '<10 Days'
		WHEN Campaign_duration BETWEEN 10 AND 39 THEN '10-40 Days'
		WHEN Campaign_duration BETWEEN 40 AND 69 THEN '40-70 Days'
		WHEN Campaign_duration BETWEEN 70 AND 99 THEN '70-100 Days'
		ELSE '>100 days' 
	END Campaign_range,
	count(*) Total_project,
	SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) Suucessful_project,
	ROUND(CAST(SUM(CASE WHEN state= 'successful' THEN 1 ELSE 0 END) AS FLOAT)/COUNT(*) * 100,2) AS Success_rate
FROM [ks-projects-201801]
GROUP BY (CASE 
		WHEN Campaign_duration <10 THEN '<10 Days'
		WHEN Campaign_duration BETWEEN 10 AND 39 THEN '10-40 Days'
		WHEN Campaign_duration BETWEEN 40 AND 69 THEN '40-70 Days'
		WHEN Campaign_duration BETWEEN 70 AND 99 THEN '70-100 Days'
		ELSE '>100 days' 
	END)
ORDER BY Success_rate DESC
```
