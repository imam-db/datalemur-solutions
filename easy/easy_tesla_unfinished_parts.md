### Unfinished Parts [Tesla SQL Interview Question]

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

| part    | finish_date         | assembly_step |
|---------|---------------------|---------------|
| battery | 01/22/2022 00:00:00 | 1             |
| battery | 02/22/2022 00:00:00 | 2             |
| battery | 03/22/2022 00:00:00 | 3             |
| bumper  | 01/22/2022 00:00:00 | 1             |
| bumper  | 02/22/2022 00:00:00 | 2             |
| bumper  |                     | 3             |
| bumper  |                     | 4             |

**Example Output**:

| **part**      |
|---------------|
| bumper        |

**Explanation**

The only item in the output is "bumper" because step 3 didn't have a finish date.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT DISTINCT part
FROM parts_assembly
WHERE finish_date IS NULL;
```