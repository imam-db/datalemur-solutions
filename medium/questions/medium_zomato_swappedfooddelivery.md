### Swapped Food Delivery [Zomato SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/sql-swapped-food-delivery">Read the original question page</a>

Zomato is a leading online food delivery service that connects users with various restaurants and cuisines, allowing them to browse menus, place orders, and get meals delivered to their doorsteps.

Recently, Zomato encountered an issue with their delivery system. Due to an error in the delivery driver instructions, each item's order was swapped with the item in the subsequent row. As a data analyst, you're asked to correct this swapping error and return the proper pairing of order ID and item.

If the last item has an odd order ID, it should remain as the last item in the corrected data. For example, if the last item is Order ID 7 Tandoori Chicken, then it should remain as Order ID 7 in the corrected data.

In the results, return the correct pairs of order IDs and items.



`orders` **Schema**:

| **column_name** | **type** | **description**                          |
|-----------------|----------|------------------------------------------|
| order_id        | integer  | The ID of each Zomato order.             |
| item            | string   | The name of the food item in each order. |

`orders` **Example Input**:

Here's a sample of the initial incorrect data:

| **order_id** | **item**         |
|--------------|------------------|
| 1            | Chow Mein        |
| 2            | Pizza            |
| 3            | Pad Thai         |
| 4            | Butter Chicken   |
| 5            | Eggrolls         |
| 6            | Burger           |
| 7            | Tandoori Chicken |

`oders` **Example Output**:

The corrected data should look like this:

| **corrected_order_id** | **item**         |
|------------------------|------------------|
| 1                      | Pizza            |
| 2                      | Chow Mein        |
| 3                      | Butter Chicken   |
| 4                      | Pad Thai         |
| 5                      | Burger           |
| 6                      | Eggrolls         |
| 7                      | Tandoori Chicken |

**Explanation**

Order ID 1 is now associated with Pizza and Order ID 2 is paired with Chow Mein. This adjustment ensures that each order is correctly aligned with its respective item, addressing the initial swapping error.

Order ID 7 remains unchanged and is still associated with Tandoori Chicken. This preserves the order sequence ensuring that the last odd order ID remains unaltered.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH q AS
(
    SELECT order_id,
    ROUND(order_id/2::DECIMAL,0) AS grp,
    COUNT(order_id) OVER(PARTITION BY ROUND(order_id/2::DECIMAL,0)) AS total,
    item
    FROM orders
    ORDER BY grp, 
    order_id DESC
)
SELECT CASE WHEN MOD(order_id,2)=0 AND total = 2 THEN order_id-1 
  WHEN MOD(order_id,2)=1 AND total =2 THEN order_id+1
  ELSE order_id END AS corrected_order_id,
  item
FROM q;
```


**Solution Output**

| **corrected_order_id** |     **item**     |
|:----------------------:|:----------------:|
| 1                      | Pizza            |
| 2                      | Chow Mein        |
| 3                      | Butter Chicken   |
| 4                      | Pad Thai         |
| 5                      | Burger           |
| 6                      | Eggrolls         |
| 7                      | Sushi            |
| 8                      | Tandoori Chicken |
| 9                      | Ramen            |
| 10                     | Tacos            |
| 11                     | Lasagna          |
| 12                     | Burrito          |
| 13                     | Steak            |
| 14                     | Salad            |
| 15                     | Spaghetti        |

