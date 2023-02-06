### Maximize Prime Item Inventory [Amazon SQL Interview Question]


<a href="https://datalemur.com/questions/prime-warehouse-storage">Read the original question page</a>

Amazon wants to maximize the number of items it can stock in a 500,000 square feet warehouse. It wants to stock as many prime items as possible, and afterwards use the remaining square footage to stock the most number of non-prime items.

Write a SQL query to find the number of prime and non-prime items that can be stored in the 500,000 square feet warehouse. Output the item type and number of items to be stocked.



`inventory` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| item_id         | integer  |
| item_type       | string   |
| item_category   | string   |
| square_footage  | decimal  |

`inventory` **Example Input**:

| **item_id** | **item_type**  | **item_category** | **square_footage** |
|-------------|----------------|-------------------|--------------------|
| 1374        | prime_eligible | mini refrigerator | 68.00              |
| 4245        | not_prime      | standing lamp     | 26.40              |
| 2452        | prime_eligible | television        | 85.00              |
| 3255        | not_prime      | side table        | 22.60              |
| 1672        | prime_eligible | laptop            | 8.50               |

**Example Output**:

| **item_type**  | **item_count** |
|----------------|----------------|
| prime_eligible | 9285           |
| not_prime      | 6              |

The dataset you are querying against may have different input & output - this is just an example!

**To prioritise storage of prime_eligible items:**

The combination of the prime_eligible items has a total square footage of 161.50 sq ft (`68.00 sq ft + 85.00 sq ft + 8.50 sq ft`).

To prioritise the storage of the prime_eligible items, we find the number of times that we can stock the combination of the prime_eligible items which are 3,095 times, mathematically expressed as: `500,000 sq ft / 161.50 sq ft = 3,095 items`

Then, we multiply 3,095 times with 3 items (because we're asked to output the number of items to stock), which gives us **9,285 items.**


**Stocking not_prime items with remaining storage space:**

After stocking the prime_eligible items, we have a remaining 157.50 sq ft (`500,000 sq ft - (3,095 times x 161.50 sq ft`).

Then, we divide by the total square footage for the combination of 2 not_prime items which is mathematically expressed as `157.50 sq ft` / `(26.40 sq ft + 22.60 sq ft) = 3 times` so the total number of not_prime items that we can stock is **6 items** `(3 times x (26.40 sq ft + 22.60 sq ft))`.

**Solution**

```sql
WITH item_type_squarefootage AS
(
  SELECT item_type,
    SUM(square_footage) AS sum_square_footage,
    COUNT(item_id) AS total_item
  FROM inventory
  GROUP BY item_type
), all_type AS
(
  SELECT item_type,
    sum_square_footage,
    500000 AS sfw,
    FLOOR(500000 / sum_square_footage) AS items,
    500000 - ((FLOOR(500000 / sum_square_footage)) * sum_square_footage) AS remain,
    FLOOR(500000 / sum_square_footage) * total_item AS item_count,
    total_item
  FROM item_type_squarefootage
  -- WHERE item_type = 'prime_eligible'
), not_prime AS
(
SELECT 
  FLOOR(remain / (SELECT sum_square_footage 
                  FROM all_type 
                  WHERE item_type = 'not_prime')) *
                  (SELECT total_item 
                  FROM all_type 
                  WHERE item_type = 'not_prime') AS item_count
FROM all_type
WHERE item_type = 'prime_eligible'
), final AS
(
  SELECT item_type,
    all_type.item_count
  FROM all_type
  WHERE item_type = 'prime_eligible'
  UNION
  SELECT 'not_prime' AS item_type,
    not_prime.item_count
  FROM not_prime
)
SELECT *
FROM final
ORDER BY item_count DESC;
```


**Solution Output**

|  **item_type** | **item_count** |
|:--------------:|:--------------:|
| prime_eligible | 5400           |
| not_prime      | 8              |

