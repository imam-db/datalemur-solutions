### User's Third Transaction [Uber SQL Interview Question]


<a href="https://datalemur.com/questions/sql-third-transaction">Read the original question page</a>

This is the same question as problem #11 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

`transactions` **Table**:

| **Column Name**  | **Type**  |
|------------------|-----------|
| user_id          | integer   |
| spend            | decimal   |
| transaction_date | timestamp |

`transactions` **Example Input**:

| **user_id** | **spend** | **transaction_date** |
|-------------|-----------|----------------------|
| 111         | 100.50    | 01/08/2022 12:00:00  |
| 111         | 55.00     | 01/10/2022 12:00:00  |
| 121         | 36.00     | 01/18/2022 12:00:00  |
| 145         | 24.99     | 01/26/2022 12:00:00  |
| 111         | 89.60     | 02/05/2022 12:00:00  |

**Example Output**:

| **user_id** | **spend** | **transaction_date** |
|-------------|-----------|----------------------|
| 111         | 89.60     | 02/05/2022 12:00:00  |

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
WITH transaction_order AS
(
SELECT *, 
  RANK() OVER(PARTITION BY user_id ORDER BY user_id, transaction_date, spend DESC) AS ranking
FROM transactions
)
SELECT user_id, spend, transaction_date
FROM transaction_order
WHERE ranking = 3;
```


**Solution Output**


| **user_id** | **spend** | **transaction_date** |
|:-----------:|:---------:|:--------------------:|
| 111         | 89.60     | 02/05/2022 12:00:00  |
| 121         | 67.90     | 04/03/2022 12:00:00  |
| 263         | 100.00    | 07/12/2022 12:00:00  |