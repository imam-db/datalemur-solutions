### Y-on-Y Growth Rate [Wayfair SQL Interview Question]

[Back to list of hard questions](../README.md)


<a href="https://datalemur.com/questions/yoy-growth-rate">Read the original question page</a>

This is the same question as problem #32 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the table below containing information on user transactions for particular products. Write a query to obtain the year-on-year growth rate for the total spend of each product for each year.

Output the year (in ascending order) partitioned by product id, current year's spend, previous year's spend and year-on-year growth rate (percentage rounded to 2 decimal places).



`user_transactions` **Table**:

| **Column Name**  | **Type** |
|------------------|----------|
| transaction_id   | integer  |
| product_id       | integer  |
| spend            | decimal  |
| transaction_date | datetime |

`user_transactions` **Example Input**:

| **transaction_id** | **product_id** | **spend** | **transaction_date** |
|--------------------|----------------|-----------|----------------------|
| 1341               | 123424         | 1500.60   | 12/31/2019 12:00:00  |
| 1423               | 123424         | 1000.20   | 12/31/2020 12:00:00  |
| 1623               | 123424         | 1246.44   | 12/31/2021 12:00:00  |
| 1322               | 123424         | 2145.32   | 12/31/2022 12:00:00  |

**Example Output**:

| **year** | **product_id** | **curr_year_spend** | **prev_year_spend** | **yoy_rate** |
|----------|----------------|---------------------|---------------------|--------------|
| 2019     | 123424         | 1500.60             |                     |              |
| 2020     | 123424         | 1000.20             | 1500.60             | -33.35       |
| 2021     | 123424         | 1246.44             | 1000.20             | 24.62        |
| 2022     | 123424         | 2145.32             | 1246.44             | 72.12        |

**Explanation**

The third row in the example output shows that the spend for product 123424 grew 24.62% from 1000.20 in 2020 to 1246.44 in 2021.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH product_yearly AS
(
  SELECT product_id,
    EXTRACT(YEAR FROM transaction_date) AS yr,
    SUM(spend) AS curr_year_spend
  FROM user_transactions
  GROUP BY product_id,
    EXTRACT(YEAR FROM transaction_date)
  ORDER BY product_id, 
    yr
), product_curr_prev AS
(
  SELECT py.*,
    LAG(curr_year_spend) OVER(PARTITION BY product_id) AS prev_year_spend
  FROM product_yearly AS py
)
SELECT yr as year,
  product_id,
  curr_year_spend,
  prev_year_spend,
  ROUND((curr_year_spend - prev_year_spend) / prev_year_spend * 100,2) AS yoy_rate
FROM product_curr_prev;
```


**Solution Output**

| **year** | **product_id** | **curr_year_spend** | **prev_year_spend** | **yoy_rate** |
|:--------:|:--------------:|:-------------------:|:-------------------:|:------------:|
| 2019     | 123424         | 1500.60             |                     |              |
| 2020     | 123424         | 1000.20             | 1500.60             | -33.35       |
| 2021     | 123424         | 1246.44             | 1000.20             | 24.62        |
| 2022     | 123424         | 2145.32             | 1246.44             | 72.12        |
| 2019     | 234412         | 1800.00             |                     |              |
| 2020     | 234412         | 1234.00             | 1800.00             | -31.44       |
| 2021     | 234412         | 889.50              | 1234.00             | -27.92       |
| 2022     | 234412         | 2900.00             | 889.50              | 226.03       |
| 2019     | 543623         | 6450.00             |                     |              |
| 2020     | 543623         | 5348.12             | 6450.00             | -17.08       |
| 2021     | 543623         | 2345.00             | 5348.12             | -56.15       |
| 2022     | 543623         | 5680.00             | 2345.00             | 142.22       |

