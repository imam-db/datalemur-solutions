### Compressed Mode [Alibaba SQL Interview Question]


<a href="https://datalemur.com/questions/alibaba-compressed-mode">Read the original question page</a>

You are trying to find the most common (aka the `mode`) number of items bought per order on Alibaba.

However, instead of doing analytics on all Alibaba orders, you have access to a summary table, which describes how many items were in an order (`item_count`), and the number of orders that had that many items (`order_occurrences`).

In case of multiple item counts, display the `item_counts` in ascending order.



`items_per_order` **Table**:

| **Column Name**   | **Type** |
|-------------------|----------|
| item_count        | integer  |
| order_occurrences | integer  |

`items_per_order` **Example Input**:

| **item_count** | **order_occurrences** |
|----------------|-----------------------|
| 1              | 500                   |
| 2              | 1000                  |
| 3              | 800                   |
| 4              | 1000                  |

**Example Output**:

| **mode** |
|----------|
| 2        |
| 4        |

**Explanation**

The most common number of `order_occurrences` is 1000. Both item counts of 2 and 4 fit this.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH order_ranking AS
(
  SELECT * ,
    RANK() OVER(ORDER BY order_occurrences DESC) AS ranking
  FROM items_per_order
)
SELECT item_count
FROM order_ranking
WHERE ranking = 1;
```


**Solution Output**

| **item_count** |
|----------------|
| 2              |
| 4              |

