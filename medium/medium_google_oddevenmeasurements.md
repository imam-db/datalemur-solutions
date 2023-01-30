### Odd and Even Measurements [Google SQL Interview Question]


<a href="https://datalemur.com/questions/odd-even-measurements">Read the original question page</a>

This is the same question as problem #28 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the table containing measurement values obtained from a Google sensor over several days. Measurements are taken several times within a given day.

Write a query to obtain the sum of the odd-numbered and even-numbered measurements on a particular day, in two different columns. Refer to the Example Output below for the output format.

Definition:

- 1st, 3rd, and 5th measurements taken **within a day** are considered odd-numbered measurements and the 2nd, 4th, and 6th measurements are even-numbered measurements.




`measurements` **Table**:

| **Column Name**   | **Type** |
|-------------------|----------|
| measurement_id    | integer  |
| measurement_value | decimal  |
| measurement_time  | datetime |

`measurements` **Example Input**:

| **measurement_id** | **measurement_value** | **measurement_time** |
|--------------------|-----------------------|----------------------|
| 131233             | 1109.51               | 07/10/2022 09:00:00  |
| 135211             | 1662.74               | 07/10/2022 11:00:00  |
| 523542             | 1246.24               | 07/10/2022 13:15:00  |
| 143562             | 1124.50               | 07/11/2022 15:00:00  |
| 346462             | 1234.14               | 07/11/2022 16:45:00  |

**Example Output**:

| **measurement_day** | **odd_sum** | **even_sum** |
|---------------------|-------------|--------------|
| 07/10/2022 00:00:00 | 2355.75     | 1662.74      |
| 07/11/2022 00:00:00 | 1124.50     | 1234.14      |

**Explanation**

On 07/11/2022, there are only two measurements. In chronological order, the first measurement (odd-numbered) is 1124.50, and the second measurement(even-numbered) is 1234.14.

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH measurements_order AS
(
  SELECT *,
    ROW_NUMBER() OVER(PARTITION BY measurement_time::DATE 
                      ORDER BY measurement_time) AS rownum
  FROM measurements
  ORDER BY measurement_time
)
SELECT mo.measurement_time::DATE AS measurement_day,
  SUM(CASE WHEN mo.rownum % 2 = 1 THEN mo.measurement_value END)  AS odd_sum,
  SUM(CASE WHEN mo.rownum % 2 = 0 THEN mo.measurement_value END)  AS even_sum
FROM measurements_order AS mo
GROUP BY measurement_time::DATE
ORDER BY measurement_time::DATE;
```


**Solution Output**

| **measurement_day** | **odd_sum** | **even_sum** |
|:-------------------:|:-----------:|:------------:|
| 07/10/2022 00:00:00 | 2355.75     | 1662.74      |
| 07/11/2022 00:00:00 | 2377.12     | 2480.70      |
| 07/12/2022 00:00:00 | 2903.40     | 1244.30      |

