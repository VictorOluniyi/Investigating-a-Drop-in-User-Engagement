# Investigating-a-Drop-in-User-Engagement

## About the Project
In this case study, a Corporate Social Network notices a sudden drop in user engagement over the past month. This Investigating User Engagement Drop in a Corporate Social Network using SQL project aims to delve into the significant decline in user engagement of products observed within a specific timeframe. I conducted a thorough analysis of the available data using SQL (Structured Query Language), the project seeks to uncover the underlying factors contributing to this drop and provide actionable insights to mitigate and reverse the trend.

## Project Objectives
1. Identify Patterns and Trends: Utilize SQL queries to examine the user engagement metrics, such as active users, session duration, interactions, and conversion rates, over the designated time period.

2. Segmentation Analysis: Divide the user base into relevant segments based on demographics, behavior, or any other pertinent criteria. This segmentation will help identify if the drop in engagement is uniform across all user groups or specific to certain segments.

3. Time Series Analysis: Employ time-based SQL queries to assess if the decline in engagement is part of a seasonal pattern or triggered by a specific event.

## 
To achieve this, the project follows these key steps:
1. Data Collection: I gathered a dummy user engagement data from the corporate social network's database. This includes data related to **user activity**, **event activity** (such as login events, messaging events, search events, events logged as users progress through a signup funnel, events around received emails) and **email activity**.
2. Data Preparation: I cleaned and preprocessed the collected data to ensure accuracy and consistency. This step involves handling missing values, transforming data formats, and structuring it for efficient analysis.
3. SQL Analysis: Utilize SQL queries to explore the dataset. Calculate engagement metrics over time, such as daily active users, average posts per user, and comments per post. Compare these metrics before and after the drop in engagement to identify trends.
4. Segmentation: I segmented users based on user types (new users vs. old users), device types (desktop, tablet, and mobile), and geographic regions. Analyze engagement patterns within different segments to identify if specific groups are more affected by the engagement drop.

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

-- 3. Calculate the Growth pattern within 3 months (user's activation) Daily Signups
SELECT DATE_TRUNC('day',created_at) AS day,
       COUNT(*) AS all_users,
       COUNT(CASE 
                  WHEN activated_at IS NOT NULL THEN user_id 
                  ELSE NULL 
                  END) AS activated_users
  FROM tutorial.yammer_users 
 WHERE created_at >= '2014-06-01'
   AND created_at < '2014-09-01'
 GROUP BY 1
 ORDER BY 1

-- 4. Cohort analysis (cohort users based on when they signed up for the product) Engagement by User Age Cohort
SELECT DATE_TRUNC('week',z.occurred_at) AS "week",
       AVG(z.age_at_event) AS "Average age during week",
       COUNT(DISTINCT CASE WHEN z.user_age > 70 THEN z.user_id ELSE NULL END) AS "10+ weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 70 AND z.user_age >= 63 THEN z.user_id ELSE NULL END) AS "9 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 63 AND z.user_age >= 56 THEN z.user_id ELSE NULL END) AS "8 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 56 AND z.user_age >= 49 THEN z.user_id ELSE NULL END) AS "7 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 49 AND z.user_age >= 42 THEN z.user_id ELSE NULL END) AS "6 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 42 AND z.user_age >= 35 THEN z.user_id ELSE NULL END) AS "5 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 35 AND z.user_age >= 28 THEN z.user_id ELSE NULL END) AS "4 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 28 AND z.user_age >= 21 THEN z.user_id ELSE NULL END) AS "3 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 21 AND z.user_age >= 14 THEN z.user_id ELSE NULL END) AS "2 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 14 AND z.user_age >= 7 THEN z.user_id ELSE NULL END) AS "1 week",
       COUNT(DISTINCT CASE WHEN z.user_age < 7 THEN z.user_id ELSE NULL END) AS "Less than a week"
  FROM (
        SELECT e.occurred_at,
               u.user_id,
               DATE_TRUNC('week',u.activated_at) AS activation_week,
               EXTRACT('day' FROM e.occurred_at - u.activated_at) AS age_at_event,
               EXTRACT('day' FROM '2014-09-01'::TIMESTAMP - u.activated_at) AS user_age
          FROM tutorial.yammer_users u
          JOIN tutorial.yammer_events e
            ON e.user_id = u.user_id
           AND e.event_type = 'engagement'
           AND e.event_name = 'login'
           AND e.occurred_at >= '2014-05-01'
           AND e.occurred_at < '2014-09-01'
         WHERE u.activated_at IS NOT NULL
       ) z

 GROUP BY 1
 ORDER BY 1
LIMIT 100

-- 5. Product Analysis (Weekly Engagement by Device Category)
SELECT DATE_TRUNC('week', occurred_at) AS week,
      COUNT(DISTINCT e.user_id) AS weekly_active_users,
      COUNT(DISTINCT CASE 
                        WHEN e.device IN ('macbook pro','lenovo thinkpad','macbook air','dell inspiron notebook',
                        'asus chromebook','dell inspiron desktop','acer aspire notebook','hp pavilion desktop','acer aspire desktop','mac mini')
                        THEN e.user_id 
                        ELSE NULL 
                        END) AS computer,
      COUNT(DISTINCT CASE 
                        WHEN e.device IN ('iphone 5','samsung galaxy s4','nexus 5','iphone 5s','iphone 4s','nokia lumia 635',
                        'htc one','samsung galaxy note','amazon fire phone') 
                        THEN e.user_id 
                        ELSE NULL 
                        END) AS phone,
      COUNT(DISTINCT CASE 
                        WHEN e.device IN ('ipad air','nexus 7','ipad mini','nexus 10','kindle fire','windows surface',
                        'samsumg galaxy tablet') 
                        THEN e.user_id 
                        ELSE NULL 
                        END) AS tablet
FROM tutorial.yammer_events e
WHERE e.event_type = 'engagement'
   AND e.event_name = 'login'
GROUP BY 1
ORDER BY 1
LIMIT 100;

SELECT DISTINCT action FROM tutorial.yammer_emails 

-- 6. Email Account
SELECT DATE_TRUNC('week', occurred_at) AS week,
       COUNT(CASE WHEN e.action = 'sent_weekly_digest' THEN e.user_id ELSE NULL END) AS weekly_emails,
       COUNT(CASE WHEN e.action = 'sent_reengagement_email' THEN e.user_id ELSE NULL END) AS reengagement_emails,
       COUNT(CASE WHEN e.action = 'email_open' THEN e.user_id ELSE NULL END) AS email_opens,
       COUNT(CASE WHEN e.action = 'email_clickthrough' THEN e.user_id ELSE NULL END) AS email_clickthroughs
  FROM tutorial.yammer_emails e
 GROUP BY 1
 ORDER BY 1
 
-- 7. Open and CT Rates Analysis
SELECT week,
       weekly_opens/CASE WHEN weekly_emails = 0 THEN 1 ELSE weekly_emails END::FLOAT AS weekly_open_rate,
       weekly_ctr/CASE WHEN weekly_opens = 0 THEN 1 ELSE weekly_opens END::FLOAT AS weekly_ctr,
       retain_opens/CASE WHEN retain_emails = 0 THEN 1 ELSE retain_emails END::FLOAT AS retain_open_rate,
       retain_ctr/CASE WHEN retain_opens = 0 THEN 1 ELSE retain_opens END::FLOAT AS retain_ctr
  FROM (
SELECT DATE_TRUNC('week',e1.occurred_at) AS week,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e1.user_id ELSE NULL END) AS weekly_emails,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e2.user_id ELSE NULL END) AS weekly_opens,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e3.user_id ELSE NULL END) AS weekly_ctr,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e1.user_id ELSE NULL END) AS retain_emails,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e2.user_id ELSE NULL END) AS retain_opens,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e3.user_id ELSE NULL END) AS retain_ctr
  FROM tutorial.yammer_emails e1
  LEFT JOIN tutorial.yammer_emails e2
    ON e2.occurred_at >= e1.occurred_at
   AND e2.occurred_at < e1.occurred_at + INTERVAL '5 MINUTE'
   AND e2.user_id = e1.user_id
   AND e2.action = 'email_open'
  LEFT JOIN tutorial.yammer_emails e3
    ON e3.occurred_at >= e2.occurred_at
   AND e3.occurred_at < e2.occurred_at + INTERVAL '5 MINUTE'
   AND e3.user_id = e2.user_id
   AND e3.action = 'email_clickthrough'
 WHERE e1.occurred_at >= '2014-06-01'
   AND e1.occurred_at < '2014-09-01'
   AND e1.action IN ('sent_weekly_digest','sent_reengagement_email')
 GROUP BY 1
       ) a
 ORDER BY 1
