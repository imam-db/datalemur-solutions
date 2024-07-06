### Unfinished Parts [Tesla SQL Interview Question]

[Back to list of easy questions](../README.md)


<a href="https://datalemur.com/questions/tesla-unfinished-parts">Read the original question page</a>

Tesla is investigating bottlenecks in their production, and they need your help to extract the relevant data. Write a SQL query that determines which parts have begun the assembly process but are not yet finished.

**Assumption**

- Table `parts_assembly` contains all parts in production.

`part_assembly` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| page_id         | integer  |
| page_name       | varchar  |
| assembly_step   | integer  |

`parts_assembly` **Example Input**:

| **part** | **finish_date**     | **assembly_step** |
|----------|---------------------|-------------------|
| battery  | 01/22/2022 00:00:00 | 1                 |
| battery  | 02/22/2022 00:00:00 | 2                 |
| battery  | 03/22/2022 00:00:00 | 3                 |
| bumper   | 01/22/2022 00:00:00 | 1                 |
| bumper   | 02/22/2022 00:00:00 | 2                 |
| bumper   |                     | 3                 |
| bumper   |                     | 4                 |

**Example Output**:

| **part** | **assembly_step** |
|----------|-------------------|
| bumper   | 3                 |
| bumper   | 4                 |

**Explanation**

The bumpers in step 3 and 4 are the only item that remains unfinished as it lacks a recorded finish date.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT part,
  assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;
```


**Solution Output**


| **part** | **assembly_step** |
|:--------:|:-----------------:|
| bumper   | 3                 |
| bumper   | 4                 |
| engine   | 5                 |