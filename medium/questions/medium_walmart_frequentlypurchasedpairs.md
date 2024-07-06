### Frequently Purchased Pairs [Walmart SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/frequently-purchased-pairs">Read the original question page</a>

This is the same question as problem #30 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the following tables on Walmart transactions and products. Find the number of **unique** product combinations that are bought together (purchased in the same transaction).

For example, if I find two transactions where apples and bananas are bought, and another transaction where bananas and soy milk are bought, my output would be 2 to represent the 2 **unique** combinations. Your output should be a **single number**.

Assumption:

- For each transaction, a maximum of 2 products is purchased.




`transactions` **Table**:

| **Column Name**  | **Type** |
|------------------|----------|
| transaction_id   | integer  |
| product_id       | integer  |
| user_id          | integer  |
| transaction_date | datetime |

`transactions` **Example Input**:

| **transaction_id** | **product_id** | **user_id** | **transaction_date** |
|--------------------|----------------|-------------|----------------------|
| 231574             | 111            | 234         | 03/01/2022 12:00:00  |
| 231574             | 444            | 234         | 03/01/2022 12:00:00  |
| 231574             | 222            | 234         | 03/01/2022 12:00:00  |
| 137124             | 111            | 125         | 03/05/2022 12:00:00  |
| 137124             | 444            | 125         | 03/05/2022 12:00:00  |


`products` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| product_id      | integer  |
| product_name    | string   |


`products` **Example Input**:

| **product_id** | **product_name** |
|----------------|------------------|
| 111            | apple            |
| 222            | soy milk         |
| 333            | instant oatmeal  |
| 444            | banana           |
| 555            | chia seed        |

**Example Output**:

| **combo_num** |
|---------------|
| 4             |

**Explanation**

There are 4 unique purchase combinations present in the example data.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH purchase_info AS (
  SELECT
    transactions.product_id,
    transactions.transaction_id,
    products.product_name
  FROM transactions
  INNER JOIN products
  	ON transactions.product_id = products.product_id
)

SELECT COUNT(*) AS combo_num
FROM purchase_info AS p1
INNER JOIN purchase_info AS p2
  ON p1.transaction_id = p2.transaction_id
  AND p1.product_id < p2.product_id;
```


**Solution Output**

| **combo_num** |
|---------------|
| 6             |

