### Cities With Completed Trades [Robinhood SQL Interview Question]

[Back to list of easy questions](../README.md)


<a href="https://datalemur.com/questions/completed-trades">Read the original question page</a>

This is the same question as problem #2 in the SQL Chapter of Ace the Data Science Interview!

You are given the tables below containing information on Robinhood trades and users. Write a query to list the top three cities that have the most completed trade orders in descending order.

Output the city and number of orders.

`trades` **Table**:

| **Column Name** | **Type**                         |
|-----------------|----------------------------------|
| order_id        | integer                          |
| user_id         | integer                          |
| price           | decimal                          |
| quantity        | integer                          |
| status          | string('Completed' ,'Cancelled') |
| timestamp       | datetime                         |

`trades` **Example Input**:

| **order_id** | **user_id** | **price** | **quantity** | **status** | **timestamp**       |
|--------------|-------------|-----------|--------------|------------|---------------------|
| 100101       | 111         | 9.80      | 10           | Cancelled  | 08/17/2022 12:00:00 |
| 100102       | 111         | 10.00     | 10           | Completed  | 08/17/2022 12:00:00 |
| 100259       | 148         | 5.10      | 35           | Completed  | 08/25/2022 12:00:00 |
| 100264       | 148         | 4.80      | 40           | Completed  | 08/26/2022 12:00:00 |
| 100305       | 300         | 10.00     | 15           | Completed  | 09/05/2022 12:00:00 |
| 100400       | 178         | 9.90      | 15           | Completed  | 09/09/2022 12:00:00 |
| 100565       | 265         | 25.60     | 5            | Completed  | 12/19/2022 12:00:00 |

`users` **Table:**

**Example Output**:

| **sender_id** | **message_count** |
|---------------|-------------------|
| 3601          | 2                 |
| 4500          | 1                 |

`users` **Example Input:**

| **user_id** | **city**      | **email**                     | **signup_date**     |
|-------------|---------------|-------------------------------|---------------------|
| 111         | San Francisco | rrok10@gmail.com              | 08/03/2021 12:00:00 |
| 148         | Boston        | sailor9820@gmail.com          | 08/20/2021 12:00:00 |
| 178         | San Francisco | harrypotterfan182@gmail.com   | 01/05/2022 12:00:00 |
| 265         | Denver        | shadower_@hotmail.com         | 02/26/2022 12:00:00 |
| 300         | San Francisco | houstoncowboy1122@hotmail.com | 06/30/2022 12:00:00 |

**Example Output:**

| **city**      | **total_orders** |
|---------------|------------------|
| San Francisco | 3                |
| Boston        | 2                |
| Denver        | 1                |

**Explanation**

In this example, San Francisco has first place with 3 orders, Boston has second place with 2 orders, and Denver has third place with 1 order.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT u.city, COUNT(t.order_id) AS total_orders
FROM trades AS t
INNER JOIN users AS u
  ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC;
```


**Solution Output**

|    **city**   | **total_orders** |
|:-------------:|:----------------:|
| San Francisco | 4                |
| Boston        | 3                |
| Denver        | 2                |
| New York      | 1                |