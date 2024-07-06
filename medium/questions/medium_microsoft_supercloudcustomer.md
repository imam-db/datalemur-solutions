### Supercloud Customer [Microsoft SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/supercloud-customer">Read the original question page</a>

A Microsoft Azure Supercloud customer is a company which buys at least 1 product from each product category.

Write a query to report the company ID which is a Supercloud customer.

*As of 5 Dec 2022, data in the customer_contracts and products tables were updated.*



`customer_contracts` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| customer_id     | integer  |
| product_id      | integer  |
| amount          | integer  |

`customer_contracts` **Example Input**:

| **customer_id** | **product_id** | **amount** |
|-----------------|----------------|------------|
| 1               | 1              | 1000       |
| 1               | 3              | 2000       |
| 1               | 5              | 1500       |
| 2               | 2              | 3000       |
| 2               | 6              | 2000       |

`products` **Table**:

| **Column Name**  | **Type** |
|------------------|----------|
| product_id       | integer  |
| product_category | string   |
| product_name     | string   |

`products` **Example Input**:

| **product_id** | **product_category** | **product_name**         |
|----------------|----------------------|--------------------------|
| 1              | Analytics            | Azure Databricks         |
| 2              | Analytics            | Azure Stream Analytics   |
| 4              | Containers           | Azure Kubernetes Service |
| 5              | Containers           | Azure Service Fabric     |
| 6              | Compute              | Virtual Machines         |
| 7              | Compute              | Azure Functions          |


**Example Output**:

| **customer_id** |
|-----------------|
| 1               |


**Explanation**

Customer 1 bought from Analytics, Containers, and Compute categories of Azure, and thus is a Supercloud customer. Customer 2 isn't a Supercloud customer, since they don't buy any container services from Azure.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**


```sql
WITH customer_category AS
(
  SELECT cc.customer_id, 
    COUNT(DISTINCT p.product_category) AS total_category
  FROM customer_contracts AS cc
  INNER JOIN products AS p
    ON cc.product_id = p.product_id
  GROUP BY cc.customer_id
)
SELECT customer_id
FROM customer_category
WHERE total_category = (SELECT COUNT(DISTINCT product_category) FROM products);
```

**Solution Output**

| **customer_id** |
|:---------------:|
| 7               |