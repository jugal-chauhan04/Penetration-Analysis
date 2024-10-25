# Spotify User Penetration Analysis

Market penetration is an important metric for understanding Spotify's performance and growth potential in different regions.  
In exploring the metrics that reflect Spotify's performance and growth potential across different regions, I came across the challenge of calculating the active user penetration rate in specific countries.

For this analysis, 'active_users' are defined based on the following criteria:

- **last_active_date**: The user must have interacted with Spotify within the last 30 days.
- **sessions**: The user must have engaged with Spotify for at least 5 sessions.
- **listening_hours**: The user must have spent at least 10 hours listening on Spotify.

Based on these conditions, the objective is to calculate the active `user_penetration_rate` using the following formula:

- **Active User Penetration Rate** = (Number of Active Spotify Users in the Country / Total users in the Country)

The total population of the country includes both active and non-active users.

The desired output should consist of `country` and `active_user_penetration_rate`, rounded to 2 decimals.

For the purpose of this analysis, I assumed the current day is **2024-01-31**.

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
```
## Explanation of the Query Structure  

1. Common Table Expressions (CTEs): Two CTEs are defined: active_users for counting active users and total_users for counting all users.
2. Filtering Conditions: In the active_users CTE, specific criteria (date range, session count, and listening hours) are applied to filter the user data.
3. Aggregation: Both CTEs aggregate data using the COUNT function, grouping by country.
4. Final Calculation: In the final SELECT statement, the active user penetration rate is calculated by dividing the active user count by the total user count for each country, with the result rounded to two decimal places.
