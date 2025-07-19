Project Overview
This project is designed to help beginners understand SQL querying and performance analysis using real-time data from SQL Mentor datasets. In this project, you will analyze user performance by creating and querying a table of user submissions. The goal is to solve a series of SQL problems to extract meaningful insights from user data.

Objectives
Learn how to use SQL for data analysis tasks such as aggregation, filtering, and ranking.
Understand how to calculate and manipulate data in a real-world dataset.
Gain hands-on experience with SQL functions like COUNT, SUM, AVG, EXTRACT(), and DENSE_RANK().
Develop skills for performance analysis using SQL by solving different types of data problems related to user performance.
Project Level: Beginner
This project is designed for beginners who are familiar with the basics of SQL and want to learn how to handle real-world data analysis problems. You'll be working with a small dataset and writing SQL queries to solve different tasks that are commonly encountered in data analytics.

SQL Mentor User Performance Dataset
The dataset consists of information about user submissions for an online learning platform. Each submission includes:

User ID
Question ID
Points Earned
Submission Timestamp
Username
This data allows you to analyze user performance in terms of correct and incorrect submissions, total points earned, and daily/weekly activity.

###Q1. List All Distinct Users and Their Stats
Description: Return the user name, total submissions, and total points earned by each user.
Expected Output: A list of users with their submission count and total points.

QUERY:
'''SELECT username,
       SUM(POINTS) AS total_points,
	   COUNT(SUBMITTED_AT) AS total_submissions 
FROM PROJECT
GROUP BY username
ORDER BY total_points DESC; 

###Q2. Calculate the Daily Average Points for Each User
Description: For each day, calculate the average points earned by each user.
Expected Output: A report showing the average points per user for each day.

'''QUERY:
WITH DAILY_AVGG AS (SELECT username,
       DATENAME(DAY,submitted_at) AS DAY,
       AVG(POINTS) AS AVG_POINTS
FROM PROJECT
GROUP BY DATENAME(DAY,submitted_at),username)

SELECT * FROM DAILY_AVGG
ORDER BY DAY,AVG_POINTS DESC;

Q3. Find the Top 3 Users with the Most Correct Submissions for Each Day
Description: Identify the top 3 users with the most correct submissions for each day.
Expected Output: A list of users and their correct submissions, ranked daily.

QUERY:

WITH RANK_POINT AS(SELECT  DATENAME(DAY,submitted_at)AS DAY,
                           username,
						   question_id,
		                   POINTS,
		                   DENSE_RANK() OVER(PARTITION BY USERNAME ORDER BY POINTS DESC) AS RANK_POINTS
                           FROM PROJECT)
SELECT * FROM RANK_POINT
WHERE RANK_POINTS<4;

Q4. Find the Top 5 Users with the Highest Number of Incorrect Submissions
Description: Identify the top 5 users with the highest number of incorrect submissions.
Expected Output: A list of users with the count of incorrect submissions.

QUERY;

SELECT TOP 5 username,
             SUM(POINTS) AS incorrect_submissions 
FROM PROJECT
WHERE points<0
GROUP BY username
ORDER BY SUM(POINTS) ASC

Q5. Find the Top 10 Performers for Each Week
Description: Identify the top 10 users with the highest total points earned each week.
Expected Output: A report showing the top 10 users ranked by total points per week.

QUERY:

WITH top10_sales AS(SELECT  username,
                            DATENAME(WEEK,submitted_at) as week,
							SUM(points) as tatal_week_wise_points 
					FROM PROJECT
GROUP BY DATENAME(WEEK,submitted_at),username),

sales AS (SELECT *,row_number() OVER(PARTITION BY week ORDER BY tatal_week_wise_points DESC ) AS rankk 
FROM top10_sales)

SELECT * FROM sales
WHERE rankk<9
ORDER BY week DESC,tatal_week_wise_points DESC;
