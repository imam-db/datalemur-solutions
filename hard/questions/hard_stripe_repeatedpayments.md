### Repeated Payments [Stripe SQL Interview Question]

[Back to list of hard questions](../README.md)


<a href="https://datalemur.com/questions/repeated-payments">Read the original question page</a>

Sometimes, payment transactions are repeated by accident; it could be due to user error, API failure or a retry error that causes a credit card to be charged twice.

Using the transactions table, identify any payments made at the same merchant with the same credit card for the same amount within 10 minutes of each other. Count such repeated payments.

**Assumptions:**    

- The first transaction of such payments should not be counted as a repeated payment. This means, if there are two transactions performed by a merchant with the same credit card and for the same amount within 10 minutes, there will only be 1 repeated payment.



`transaction` **Table**:

| **Column Name**       | **Type** |
|-----------------------|----------|
| transaction_id        | integer  |
| merchant_id           | integer  |
| credit_card_id        | integer  |
| amount                | integer  |
| transaction_timestamp | datetime |

`transaction` **Example Input**:

| **transaction_id** | **merchant_id** | **credit_card_id** | **amount** | **transaction_timestamp** |
|--------------------|-----------------|--------------------|------------|---------------------------|
| 1                  | 101             | 1                  | 100        | 09/25/2022 12:00:00       |
| 2                  | 101             | 1                  | 100        | 09/25/2022 12:08:00       |
| 3                  | 101             | 1                  | 100        | 09/25/2022 12:28:00       |
| 4                  | 102             | 2                  | 300        | 09/25/2022 12:00:00       |
| 6                  | 102             | 2                  | 400        | 09/25/2022 14:00:00       |

**Example Output**:

| **payment_count** |
|-------------------|
| 1                 |

**Explanation**

Within 10 minutes after Transaction 1, Transaction 2 is conducted at Merchant 1 using the same credit card for the same amount. This is the only instance of repeated payment in the given sample data.

Since Transaction 3 is completed after Transactions 2 and 1, each of which occurs after 20 and 28 minutes, respectively hence it does not meet the repeated payments' conditions. Whereas, Transactions 4 and 6 have different amounts.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH cte AS
(
  SELECT * , 
    LAG(transaction_timestamp) OVER(PARTITION BY merchant_id, credit_card_id, amount) AS time_lag
  FROM transactions
),
cte2 AS
(
  SELECT *,
    EXTRACT(EPOCH FROM (transaction_timestamp - time_lag)) AS diff_sec
  FROM cte
), cte_final AS
(
  SELECT *,
    CASE WHEN diff_sec <= 60*10 THEN 1 ELSE 0 END AS under_10_minutes
  FROM cte2
)
SELECT SUM(under_10_minutes) AS payment_count
FROM cte_final;
```


**Solution Output**

| **payment_count** |
|-------------------|
| 4                 |