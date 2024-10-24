# Spotify User Penetration Analysis

## Question

Market penetration is an important metric for understanding Spotify's performance and growth potential in different regions. You are part of the analytics team at Spotify and are tasked with calculating the active user penetration rate in specific countries.

For this task, 'active_users' are defined based on the following criteria:

- **last_active_date**: The user must have interacted with Spotify within the last 30 days.
- **sessions**: The user must have engaged with Spotify for at least 5 sessions.
- **listening_hours**: The user must have spent at least 10 hours listening on Spotify.

Based on the condition above, calculate the active `user_penetration_rate` using the following formula:

- **Active User Penetration Rate** = (Number of Active Spotify Users in the Country / Total users in the Country)

Total population of the country is based on both active and non-active users.

The output should contain `country` and `active_user_penetration_rate` rounded to 2 decimals.

Assuming the current day is **2024-01-31**.

## SQL Query

```sql
-- Define a Common Table Expression (CTE) to calculate active users
WITH active_users AS (
    -- Select the country and count of active users
    SELECT country, COUNT(user_id) AS active_count
    FROM penetration_analysis
    -- Filter users who were active within the last 30 days
    WHERE last_active_date BETWEEN '2024-01-01' AND '2024-01-31'
    -- Ensure users have at least 5 sessions and 10 listening hours
    AND sessions >= 5
    AND listening_hours >= 10
    -- Group results by country to get active user count per country
    GROUP BY country
),

-- Define another CTE to calculate total users
total_users AS (
    -- Select the country and count of all users (active and non-active)
    SELECT country, COUNT(user_id) AS total_count
    FROM penetration_analysis
    -- Group results by country to get total user count per country
    GROUP BY country
)

-- Final selection to calculate active user penetration rate
SELECT 
    t.country, 
    -- Calculate the penetration rate and round it to 2 decimal places
    ROUND(1.0 * SUM(a.active_count) / SUM(t.total_count), 2) AS active_user_penetration_rate
FROM 
    total_users t
-- Join active_users and total_users on the country
JOIN 
    active_users a ON t.country = a.country
-- Group by country to ensure each country appears once in the result
GROUP BY 
    t.country;
