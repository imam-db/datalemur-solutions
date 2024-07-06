### Advertiser Status [Facebook SQL Interview Question]

[Back to list of hard questions](../README.md)


<a href="https://datalemur.com/questions/updated-status">Read the original question page</a>

Write a query to update the Facebook advertiser's status using the `daily_pay` table. Advertiser is a two-column table containing the user id and their payment status based on the last payment and `daily_pay` table has current information about their payment. Only advertisers who paid will show up in this table.

Output the user id and current payment status sorted by the user id.



`advertiser` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| user_id         | string   |
| status          | string   |

`advertiser` **Example Input**:

| **user_id** | **status** |
|-------------|------------|
| bing        | NEW        |
| yahoo       | NEW        |
| alibaba     | EXISTING   |

`daily_pay` **Table:**

| **Column Name** | **Type** |
|-----------------|----------|
| user_id         | string   |
| paid            | decima   |

`daily_pay` **Example Input**:

| **user_id** | **paid** |
|-------------|----------|
| yahoo       | 45.00    |
| alibaba     | 100.00   |
| target      | 13.00    |

Definition of advertiser status:

-    **New**: newly registered users who made their first payment.
-    **Existing**: users who paid previously and recently made a current payment.
-    **Churn**: users who paid previously, but have yet to make any recent payment.
-    **Resurrect**: users who did not pay recently but may have made a previous payment and have made payment again recently.

**Example Output**

| **user_id** | **new_status** |
|-------------|----------------|
| bing        | CHURN          |
| yahoo       | EXISTING       |
| alibaba     | EXISTING       |

Bing's updated status is CHURN because no payment was made in the `daily_pay` table whereas Yahoo which made a payment is updated as EXISTING.

The dataset you are querying against may have different input & output - **this is just an example!**

**Read this before proceeding to solve the question**

For better understanding of the advertiser's status, we're sharing with you a table of possible transitions based on the payment status.

| **#** | **Start** | **End**   | **Condition**   |
|-------|-----------|-----------|-----------------|
| 1     | NEW       | EXISTING  | Paid on day T   |
| 2     | NEW       | CHURN     | No pay on day T |
| 3     | EXISTING  | EXISTING  | Paid on day T   |
| 4     | EXISTING  | CHURN     | No pay on day T |
| 5     | CHURN     | RESURRECT | Paid on day T   |
| 6     | CHURN     | CHURN     | No pay on day T |
| 7     | RESURRECT | EXISTING  | Paid on day T   |
| 8     | RESURRECT | CHURN     | No pay on day T |


1. Row 2, 4, 6, 8: As long as the user has not paid on day T, the end status is updated to CHURN regardless of the previous status.
2. Row 1, 3, 5, 7: When the user paid on day T, the end status is updated to either EXISTING or RESURRECT, depending on their previous state. RESURRECT is only possible when the previous state is CHURN. When the previous state is anything else, the status is updated to EXISTING.



**Solution**

```sql
WITH payment_status AS (
SELECT
  advertiser.user_id,
  advertiser.status,
  payment.paid
FROM advertiser
LEFT JOIN daily_pay AS payment
  ON advertiser.user_id = payment.user_id

UNION

SELECT
  payment.user_id,
  advertiser.status,
  payment.paid
FROM daily_pay AS payment
LEFT JOIN advertiser
  ON advertiser.user_id = payment.user_id
)

SELECT
  user_id,
  CASE WHEN paid IS NULL THEN 'CHURN'
  	WHEN status != 'CHURN' AND paid IS NOT NULL THEN 'EXISTING'
  	WHEN status = 'CHURN' AND paid IS NOT NULL THEN 'RESURRECT'
  	WHEN status IS NULL THEN 'NEW'
  END AS new_status
FROM payment_status
ORDER BY user_id;
```


**Solution Output**

| **user_id** | **new_status** |
|:-----------:|:--------------:|
| alibaba     | EXISTING       |
| baidu       | CHURN          |
| bing        | CHURN          |
| chase       | CHURN          |
| fitdata     | NEW            |
| morgan      | EXISTING       |
| target      | RESURRECT      |
| tesla       | CHURN          |

