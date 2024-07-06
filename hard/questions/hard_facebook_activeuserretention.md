### Active User Retention [Facebook SQL Interview Question]

[Back to list of hard questions](../README.md)


<a href="https://datalemur.com/questions/user-retention">Read the original question page</a>

This is the same question as problem #23 in the SQL Chapter of Ace the Data Science Interview!

Assume you have the table below containing information on Facebook user actions. Write a query to obtain the active user retention in July 2022. Output the month (in numerical format 1, 2, 3) and the number of monthly active users (MAUs).

Hint: An active user is a user who has user action ("sign-in", "like", or "comment") in the current month and last month.



`user_actions` **Table**:

| **Column Name** | **Type**                             |
|-----------------|--------------------------------------|
| user_id         | integer                              |
| event_id        | integer                              |
| event_type      | string ("sign-in, "like", "comment") |
| event_date      | datetime                             |

`user_actions` **Example Input**:

| **user_id** | **event_id** | **event_type** | **event_date**      |
|-------------|--------------|----------------|---------------------|
| 445         | 7765         | sign-in        | 05/31/2022 12:00:00 |
| 742         | 6458         | sign-in        | 06/03/2022 12:00:00 |
| 445         | 3634         | like           | 06/05/2022 12:00:00 |
| 742         | 1374         | comment        | 06/05/2022 12:00:00 |
| 648         | 3124         | like           | 06/18/2022 12:00:00 |

**Example Output for June 2022**:

| **month** | **monthly_active_users** |
|-----------|--------------------------|
| 6         | 1                        |

**Explanation**

In June 2022, there was only one monthly active user (MAU), user_id 445.

*Note: We are showing you output for June 2022 as the user_actions table only have event_dates in June 2022. You should work out the solution for July 2022.*

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH user_period
AS
(
  SELECT DISTINCT ua.user_id,
    EXTRACT(MONTH FROM ua.event_date) AS ua_month
  FROM user_actions AS ua
  ORDER BY ua.user_id, ua_month
),
user_prevmonth AS
(
  SELECT *,
    LAG(ua_month) OVER(PARTITION BY user_id) AS prev_month
  FROM user_period
),
user_retention AS
(
  SELECT user_id, ua_month
  FROM user_prevmonth
  WHERE ua_month - prev_month = 1
)
SELECT ua_month AS mth,
  COUNT(user_id) AS monthly_active_users
FROM user_retention
WHERE ua_month = 7
GROUP BY ua_month;
```


**Solution Output**

| **mth** | **monthly_active_users** |
|:-------:|:------------------------:|
| 7       | 2                        |

