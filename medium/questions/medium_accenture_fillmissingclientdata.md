### Fill Missing Client Data [Accenture SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/fill-missing-product">Read the original question page</a>

When you log in to your retailer client's database, you notice that their product catalog data is full of gaps in the category column. Can you write a SQL query that returns the product catalog with the missing data filled in?

Assumptions

- Each category is mentioned only once in a category column.
- All the products belonging to same category are grouped together.
- The first product from a product group will always have a defined category.
    - Meaning that the first item from each category will not have a missing category value.



`products` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| product_id      | integer  |
| category        | varchar  |
| name            | varchar  |

`products` **Example Input**:

| **product_id** | **category** | **name**              |
|----------------|--------------|-----------------------|
| 1              | Shoes        | Sperry Boat Shoe      |
| 2              |              | Adidas Stan Smith     |
| 3              |              | Vans Authentic        |
| 4              | Jeans        | Levi 511              |
| 5              |              | Wrangler Straight Fit |
| 6              | Shirts       | Lacoste Classic Polo  |
| 7              |              | Nautica Linen Shirt   |

**Example Output**:

| **product_id** | **category** | **name**              |
|----------------|--------------|-----------------------|
| 1              | Shoes        | Sperry Boat Shoe      |
| 2              | Shoes        | Adidas Stan Smith     |
| 3              | Shoes        | Vans Authentic        |
| 4              | Jeans        | Levi 511              |
| 5              | Jeans        | Wrangler Straight Fit |
| 6              | Shirts       | Lacoste Classic Polo  |
| 7              | Shirts       | Nautica Linen Shirt   |

**Explanation**

Shoes will replace all `NULL` values below the product Sperry Boat Shoe until Jeans appears. Similarly, Jeans will replace `NULL`s for the product Wrangler Straight Fit, and so on.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH products_category
AS
(
	SELECT product_id,
		category,
		"name",
		COUNT(category) OVER(ORDER BY product_id) AS group_category
	FROM public.products
)
SELECT *,
	FIRST_VALUE(category) OVER(PARTITION BY group_category) AS new_category
FROM products_category;
```


**Solution Output**

| **product_id** | **category** | **name**              | **group_category** | **new_category** |
|----------------|--------------|-----------------------|--------------------|------------------|
| 1              | Shoes        | Sperry Boat Shoe      | 1                  | Shoes            |
| 2              |              | Adidas Stan Smith     | 1                  | Shoes            |
| 3              |              | Vans Authentic        | 1                  | Shoes            |
| 4              | Jeans        | Levi 511              | 2                  | Jeans            |
| 5              |              | Wrangler Straight Fit | 2                  | Jeans            |
| 6              | Shirts       | Lacoste Classic Polo  | 3                  | Shirts           |
| 7              |              | Nautica Linen Shirt   | 3                  | Shirts           |
| 8              | Jackets      | Belstaff Blue Fit     | 4                  | Jackets          |
| 9              |              | Uniqlo Comfort Jacket | 4                  | Jackets          |

