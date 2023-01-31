### Compressed Mean [Alibaba SQL Interview Question]


<a href="https://datalemur.com/questions/alibaba-compressed-mean">Read the original question page</a>

You are trying to find the mean number of items bought per order on Alibaba, rounded to 1 decimal place.

However, instead of doing analytics on all Alibaba orders, you have access to a summary table, which describes how many items were in an order (`item_count`), and the number of orders that had that many items (`order_occurrences`).

`items_per_order` **Table**:

| **Column Name**  | **Type** |
|------------------|----------|
| item_count       | integer  |
| order_occurences | integer  |

`items_per_order` **Example Input**:

| **item_count** | **order_occurrences** | 
|----------------|-----------------------|
| 1              | 500                   |
| 2              | 1000                  |
| 3              | 800                   |
| 4              | 1000                  |

There are 500 orders with 1 item in each order; 1000 orders with 2 items in each order; 800 orders with 3 items in each order.

**Example Output**:

| **mean**  | 
|-----------|
| 2.7       |

**Explanation**

Let's calculate the arithmetic average:

Total items = `(1*500) + (2*1000) + (3*800) + (4*1000) = 8900`

Total orders = `500 + 1000 + 800 + 1000 = 3300`

Mean = `8900 / 3300 = 2.7`

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT ROUND(SUM(item_count*order_occurrences)::DECIMAL / 
  SUM(order_occurrences),1) AS mean
FROM items_per_order;
```

**Solution Output**

| **mean** |
|:--------:|
| 3.9      |