# Investigating-a-Drop-in-User-Engagement

## About the Project
In this case study, a Corporate Social Network notices a sudden drop in user engagement over the past month. This Investigating User Engagement Drop in a Corporate Social Network using SQL project aims to delve into the significant decline in user engagement of products observed within a specific timeframe. I conducted a thorough analysis of the available data using SQL (Structured Query Language), the project seeks to uncover the underlying factors contributing to this drop and provide actionable insights to mitigate and reverse the trend.

## Project Objectives
1. Identify Patterns and Trends: Utilize SQL queries to examine the user engagement metrics, such as active users, session duration, interactions, and conversion rates, over the designated time period.
2. Segmentation Analysis: Divide the user base into relevant segments based on demographics, behavior, or any other pertinent criteria. This segmentation will help identify if the drop in engagement is uniform across all user groups or specific to certain segments.
3. Time Series Analysis: Employ time-based SQL queries to assess if the decline in engagement is part of a seasonal pattern or triggered by a specific event.

## Approach
To achieve this, the project follows these key steps:
1. Data Collection: I gathered dummy user engagement data from the corporate social network's database. This includes data related to **user activity**, **event activity** (such as login events, messaging events, search events, events logged as users progress through a signup funnel, events around received emails) and **email activity**.
2. Data Preparation: I cleaned and preprocessed the collected data to ensure accuracy and consistency. This step involves handling missing values, transforming data formats, and structuring it for efficient analysis.
3. SQL Analysis: Utilize SQL queries to explore the dataset. Calculate engagement metrics over time, such as daily active users, average posts per user, and comments per post. Compare these metrics before and after the drop in engagement to identify trends.
   #### i. Identify Patterns and Trends: I utilized SQL queries to examine the user engagement metrics, such as active users, session duration, and interactions, over the designated time period. I noticed nothing really changed about the growth rate, it continues to be high during the week, low on weekends.
![carbon (1)](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/e9b79377-d8e3-4f2c-8f25-a21be6671fa1)
![first](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/808e2b3e-4543-4304-aed3-741678382eb0)
  #### ii. Segmentation Analysis: I segmented users based on user types (new users vs. old users), device types (desktop, tablet, and mobile), and geographic regions. Analyze engagement patterns within different segments to identify if specific groups are more affected by the engagement drop. This chart shows a decrease in engagement among users who signed up more than 10 weeks prior indicating the problem is localized to older users.
![carbon](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/5d97c43e-27ef-4925-89ae-5d72f843ba65)
![Segmentation](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/f9ad48c4-5179-476d-8585-d8903e0e7dec)
  #### iii. Product Comparison: I compared the products that experienced a decline in engagement with those that remained consistent or improved. This will aid in pinpointing which particular product might be causing the drop. there was a pretty steep drop in phone engagement rates. So it's likely that there's a problem with the mobile app related to long-time user retention.
![carbon (2)](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/609446cc-55c3-43b7-8c85-c80dd53c1888)
![ENGAGEMENT](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/c2cb2406-f4e3-423d-8bae-1a635af90d0f)
  #### iv. Email Engagement Analysis: I examined various metrics and data related to email interactions, such as open rates, click-through rates, conversion rates, unsubscribe rates, and more to gain insights into user behavior, preferences, and trends, helping them identify the causes behind a drop in engagement and take appropriate actions to improve it. The analysis shows the email clickthroughs are way down, indicating clearly that the problem has to do with digest emails in addition to mobile apps.
![Victor](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/29fd2b86-8d4a-48bd-8796-b1682d45d61d)
![email](https://github.com/VictorOluniyi/Investigating-a-Drop-in-User-Engagement/assets/115374063/c0a367f6-7815-4ea7-bb3b-89a50632aad0)

## Conclusion:
The investigation, powered by SQL analysis, provided a comprehensive understanding of the user engagement drop. By identifying critical issues, the project empowered the company to make data-backed decisions and implement targeted solutions. This case study demonstrates the value of data-driven insights in diagnosing and remedying challenges within digital platforms.

-- 1. Total engagement per user
SELECT user_id,
  COUNT(event_type) AS total_engage
FROM tutorial.yammer_events
WHERE event_type = 'engagement'
GROUP BY user_id
ORDER BY total_engage DESC;

-- 2. Engage trend over time
SELECT DATE(occurred_at) AS date, 
    COUNT(*) AS total_engagements
FROM tutorial.yammer_events
WHERE event_type = 'engagement'
GROUP BY date
ORDER BY date DESC;
