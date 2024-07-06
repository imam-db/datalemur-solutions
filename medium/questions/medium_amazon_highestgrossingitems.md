### Highest-Grossing Items [Amazon SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/sql-highest-grossing">Read the original question page</a>

This is the same question as problem #12 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the table containing information on Amazon customers and their spending on products in various categories.

Identify the top two highest-grossing products within each category in 2022. Output the category, product, and total spend.


`product_spend` **Table**:

| **Column Name**  | **Type**  |
|------------------|-----------|
| category         | string    |
| product          | string    |
| user_id          | integer   |
| spend            | decimal   |
| transaction_date | timestamp |

`product_spend` **Example Input**:

| **category** | **product**      | **user_id** | **spend** | **transaction_date** |
|--------------|------------------|-------------|-----------|----------------------|
| appliance    | refrigerator     | 165         | 246.00    | 12/26/2021 12:00:00  |
| appliance    | refrigerator     | 123         | 299.99    | 03/02/2022 12:00:00  |
| appliance    | washing machine  | 123         | 219.80    | 03/02/2022 12:00:00  |
| electronics  | vacuum           | 178         | 152.00    | 04/05/2022 12:00:00  |
| electronics  | wireless headset | 156         | 249.90    | 07/08/2022 12:00:00  |
| electronics  | vacuum           | 145         | 189.00    | 07/15/2022 12:00:00  |


**Example Output**:

| **category** | **product**      | **total_spend** |
|--------------|------------------|-----------------|
| appliance    | refrigerator     | 299.99          |
| appliance    | washing machine  | 219.80          |
| electronics  | vacuum           | 341.00          |
| electronics  | wireless headset | 249.90          |


The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
WITH category_product AS
(
SELECT category, 
        product, 
        SUM(spend) AS total_spend
FROM product_spend
WHERE transaction_date BETWEEN '2022-01-01' AND '2022-12-31'
GROUP BY category, 
  product
),
product_sorting AS
(
SELECT category, 
        product, 
        total_spend, 
        RANK() OVER(PARTITION BY category ORDER BY total_spend DESC) AS ranking
FROM category_product
)

SELECT category, 
        product, 
        total_spend
FROM product_sorting
WHERE ranking IN (1,2);
```


**Solution Output**


| **category** |    **product**   | **total_spend** |
|:------------:|:----------------:|:---------------:|
| appliance    | washing machine  | 439.80          |
| appliance    | refrigerator     | 299.99          |
| electronics  | vacuum           | 486.66          |
| electronics  | wireless headset | 447.90          |