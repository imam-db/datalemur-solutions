### Pharmacy Analytics (Part 2) [CVS Health SQL Interview Question]


<a href="https://datalemur.com/questions/non-profitable-drugs">Read the original question page</a>

CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.

Write a query to find out which manufacturer is associated with the drugs that were not profitable and how much money CVS lost on these drugs. 

Output the manufacturer, number of drugs and total losses. Total losses should be in absolute value. Display the results with the highest losses on top.

If you like this question, try out Pharmacy Analytics (Part 3)!

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

| **product_id** | **units_sold** | **total_sales** | **cogs**   | **manufacturer** | **drug**                  |
|----------------|----------------|-----------------|------------|------------------|---------------------------|
| 156            | 89514          | 3130097.00      | 3427421.73 | Biogen           | Acyclovir                 |
| 25             | 222331         | 2753546.00      | 2974975.36 | AbbVie           | Lamivudine and Zidovudine |
| 50             | 90484          | 2521023.73      | 2742445.90 | Eli Lilly        | Dermasorb TA Complete Kit |
| 98             | 110746         | 813188.82       | 140422.87  | Biogen           | Medi-Chord                |

**Example Output**:

| **manufacturer** | **drug_count** | **total_loss** |
|------------------|----------------|----------------|
| Biogen           | 1              | 297324.73      |
| AbbVie           | 1              | 221429.36      |
| Eli Lilly        | 1              | 221422.17      |

**Explanation**

The drugs in the first 3 rows of the **Example Input** table reported losses. Biogen's losses were the highest followed by AbbVie's and Eli Lilly's.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
WITH manufacturer_drug AS
(
SELECT manufacturer,
  drug,
  SUM(total_sales) - SUM(cogs) AS total_loss
FROM pharmacy_sales
GROUP BY manufacturer, 
  drug
HAVING SUM(total_sales) - SUM(cogs)  < 0
ORDER BY total_loss
)
SELECT manufacturer,
  COUNT(drug) AS drug_count,
  SUM(total_loss) * -1 AS total_loss
FROM manufacturer_drug
GROUP BY manufacturer
ORDER BY total_loss DESC;
```

**Solution Output**

|  **manufacturer** | **drug_count** | **total_loss** |
|:-----------------:|:--------------:|:--------------:|
| Johnson & Johnson | 6              | 894913.13      |
| Eli Lilly         | 4              | 447352.90      |
| Biogen            | 3              | 417018.89      |
| AbbVie            | 2              | 413991.10      |
| Roche             | 2              | 159741.62      |
| Bayer             | 1              | 28785.28       |