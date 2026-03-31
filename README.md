# Calculating-Free-to-Paid-Conversion-Rate-with-SQL
## Project Overview
When students are watching the first session they get engaged with that session. After watching that session we calculate the coversion of student who actually purchased the subscription. That exact coversion is calculated after watching the session. 
This project focuses on the basic concepts to calculate the conversion rate. 

## Project Objectives
Explains the free to paid conversion rate of students who have watched a lecture. 
Give the average duration between the date of first-time engagement and when a student purchases a subscription for the first time.

## Key Questions Answered
- What is the free to paid conversion rate of students who have watched a lecture?
- What is the average duration between the registration date and when a student has watched a lecture for the first time (date of first-time engagement)?
- What is the average duration between the date of first time engagement and when a student purchases a subscription for the first time (date of first-time purchase)?
  
## Dataset Description
The dataset contain complete students data in the form sql queries

## Tools Used
-SQL (MySQL Workbench)

## SQL Analysis & Queries

```sql
USE db_course_conversions;

SELECT 
    e.student_id,
    i.date_registered,
    MIN(e.date_watched) AS first_date_watched,
    MIN(p.date_purchased) AS first_date_purchased,
    DATEDIFF(MIN(e.date_watched), i.date_registered) AS days_diff_reg_watch,
    DATEDIFF(MIN(p.date_purchased),
            MIN(e.date_watched)) AS days_diff_watch_purch
FROM
    student_engagement e
        JOIN
    student_info i ON e.student_id = i.student_id
        LEFT JOIN
    student_purchases p ON e.student_id = p.student_id
GROUP BY e.student_id
HAVING first_date_purchased IS NULL
    OR first_date_watched <= first_date_purchased;

SELECT 
    ROUND(COUNT(first_date_purchased) / COUNT(first_date_watched),
            2) * 100 AS conversion_rate,
    ROUND(SUM(days_diff_reg_watch) / COUNT(days_diff_reg_watch),
            2) AS av_reg_watch,
    ROUND(SUM(days_diff_watch_purch) / COUNT(days_diff_watch_purch),
            2) AS av_watch_purch
FROM
    (
    SELECT 
        e.student_id,
		i.date_registered,
		MIN(e.date_watched) AS first_date_watched,
		MIN(p.date_purchased) AS first_date_purchased,
		DATEDIFF(MIN(e.date_watched), i.date_registered) AS days_diff_reg_watch,
		DATEDIFF(MIN(p.date_purchased), MIN(e.date_watched)) AS days_diff_watch_purch
    FROM
        student_engagement e
    JOIN student_info i ON e.student_id = i.student_id
    LEFT JOIN student_purchases p ON e.student_id = p.student_id
    GROUP BY e.student_id
    HAVING first_date_purchased IS NULL
        OR first_date_watched <= first_date_purchased) a;
```
## Answers :
- Conversion Rate : 11
- Average duration between the registration date and when a student has watched a lecture for the first time : 3.42
- Average duration between the date of first time engagement and when a student purchases a subscription for the first time : 26.25
