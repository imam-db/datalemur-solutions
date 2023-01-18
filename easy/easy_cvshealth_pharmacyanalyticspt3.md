### Pharmacy Analytics (Part 3) [CVS Health SQL Interview Question]


<a href="https://datalemur.com/questions/total-drugs-sales">Read the original question page</a>

CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.

Write a query to find the total sales of drugs for each manufacturer. Round your answer to the closest million, and report your results in descending order of total sales.

Because this data is being directly fed into a dashboard which is being seen by business stakeholders, format your result like this: "$36 million".

If you like this question, try out Pharmacy Analytics (Part 4)!

`pharmacy_sales` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| product_id      | integer  |
| units_sold      | integer  |
| total_sales     | decimal  |
| cogs            | decimal  |
| manufacturer    | varchar  |
| drug            | varchar  |

`pharmacy_sales` **Example Input**:

| **product_id** | **units_sold** | **total_sales** | **cogs**   | **manufacturer** | **drug**        |
|----------------|----------------|-----------------|------------|------------------|-----------------|
| 94             | 132362         | 2041758.41      | 1373721.70 | Biogen           | UP and UP       |
| 9              | 37410          | 293452.54       | 208876.01  | Eli Lilly        | Zyprexa         |
| 50             | 90484          | 2521023.73      | 2742445.9  | Eli Lilly        | Dermasorb       |
| 61             | 77023          | 500101.61       | 419174.97  | Biogen           | Varicose Relief |
| 136            | 144814         | 1084258.00      | 1006447.73 | Biogen           | Burkhart        |

**Example Output**:

| **manufacturer** | **sale**   |
|------------------|------------|
| Biogen           | $4 million |
| Eli Lilly        | $3 million |

**Explanation**

The total sales for Biogen is $4 million ($2,041,758.41 + $500,101.61 + $1,084,258.00 = $3,626,118.02) and for Eli Lilly is $3 million ($293,452.54 + $2,521,023.73 = $2,814,476.27).

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT manufacturer,
  CONCAT('$',ROUND(SUM(total_sales)/1000000,0), ' million') AS sale
FROM pharmacy_sales
GROUP BY manufacturer
ORDER BY ROUND(SUM(total_sales)/1000000,0) DESC;
```

**Solution Output**

|  **manufacturer** |   **sale**   |
|:-----------------:|:------------:|
| AbbVie            | $114 million |
| Eli Lilly         | $82 million  |
| Biogen            | $70 million  |
| Johnson & Johnson | $43 million  |
| Bayer             | $34 million  |
| AstraZeneca       | $32 million  |
| Pfizer            | $28 million  |
| Merck             | $27 million  |
| Novartis          | $26 million  |
| Sanofi            | $25 million  |
| Roche             | $16 million  |
| GlaxoSmithKline   | $4 million   |