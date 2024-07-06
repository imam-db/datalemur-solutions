### Patient Support Analysis (Part 2) [UnitedHealth SQL Interview Question]

[Back to list of easy questions](../README.md)


<a href="https://datalemur.com/questions/uncategorized-calls-percentage">Read the original question page</a>

UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Calls to the Advocate4Me call centre are categorised, but sometimes they can't fit neatly into a category. These uncategorised calls are labelled “n/a”, or are just empty (when a support agent enters nothing into the category field).

Write a query to find the percentage of calls that cannot be categorised. Round your answer to 1 decimal place.

`callers` **Table**:

| **Column Name**    | **Type**  |
|--------------------|-----------|
| policy_holder_id   | integer   |
| case_id            | varchar   |
| call_category      | varchar   |
| call_received      | timestamp |
| call_duration_secs | integer   |
| original_order     | integer   |

`callers` **Example Input**:

| **policy_holder_id** | **case_id**         | **call_category** | **call_received**   | **call_duration_secs** | **original_order** |
|----------------------|---------------------|-------------------|---------------------|------------------------|--------------------|
| 52481621             | a94c-2213-4ba5-812d |                   | 01/17/2022 19:37:00 | 286                    | 161                |
| 51435044             | f0b5-0eb0-4c49-b21e | n/a               | 01/18/2022 2:46:00  | 208                    | 225                |
| 52082925             | 289b-d7e8-4527-bdf5 | benefits          | 01/18/2022 3:01:00  | 291                    | 352                |
| 54624612             | 62c2-d9a3-44d2-9065 | IT_support        | 01/19/2022 0:27:00  | 273                    | 358                |
| 54624612             | 9f57-164b-4a36-934e | claims            | 01/19/2022 6:33:00  | 157                    | 362                |

**Example Output**:

| **call_percentage** |
|---------------------|
| 40.0                |

**Explanation**

A total of 5 calls were registered. Out of which 2 calls were not categorised. That makes 40.0% (2/5 x 100.0) of the calls uncategorised.

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
SELECT ROUND(SUM(CASE WHEN c.call_category IN ('','n/a') OR c.call_category IS NULL THEN 1 ELSE 0 END)::DECIMAL
              / COUNT(policy_holder_id) * 100,1) AS uncategorize_call_pct 
FROM callers AS c;
```

**Solution Output**

| **call_percentage** |
|:-------------------:|
| 45.0                |