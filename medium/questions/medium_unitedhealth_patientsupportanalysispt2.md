### Patient Support Analysis (Part 2) [UnitedHealth SQL Interview Question]

[Back to list of medium questions](../README.md)

<a href="https://datalemur.com/questions/uncategorized-calls-percentage">Read the original question page</a>

UnitedHealth Group (UHG) has a program called Advocate4Me, which allows policy holders (or, members) to call an advocate and receive support for their health care needs – whether that's claims and benefits support, drug coverage, pre- and post-authorisation, medical records, emergency assistance, or member portal services.

Calls to the Advocate4Me call centre are classified into various categories, but some calls cannot be neatly categorised. These uncategorised calls are labeled as “n/a”, or are left empty when the support agent does not enter anything into the call category field.

Write a query to calculate the percentage of calls that cannot be categorised. Round your answer to 1 decimal place. For example, 45.0, 48.5, 57.7.



`callers` **Table**:

| **Column Name**    | **Type**  |
|--------------------|-----------|
| policy_holder_id   | integer   |
| case_id            | varchar   |
| call_category      | varchar   |
| call_date          | timestamp |
| call_duration_secs | integer   |

`callers` **Example Input**:

| **policy_holder_id** | **case_id**                          | **call_category**    | **call_date**        | **call_duration_secs** |
|----------------------|--------------------------------------|----------------------|----------------------|------------------------|
| 1                    | f1d012f9-9d02-4966-a968-bf6c5bc9a9fe | emergency assistance | 2023-04-13T19:16:53Z | 144                    |
| 1                    | 41ce8fb6-1ddd-4f50-ac31-07bfcce6aaab | authorisation        | 2023-05-25T09:09:30Z | 815                    |
| 2                    | 9b1af84b-eedb-4c21-9730-6f099cc2cc5e | n/a                  | 2023-01-26T01:21:27Z | 992                    |
| 2                    | 8471a3d4-6fc7-4bb2-9fc7-4583e3638a9e | emergency assistance | 2023-03-09T10:58:54Z | 128                    |
| 2                    | 38208fae-bad0-49bf-99aa-7842ba2e37bc | benefits             | 2023-06-05T07:35:43Z | 619                    |

**Example Output**:

| **uncategorised_call_pct** |
|----------------------------|
| 20.0                       |

**Explanation**

Out of the total of 5 calls registered, one call was not categorised. Therefore, the percentage of uncategorised calls is calculated as 20.0% (1 out of 5 multiplied by 100 and rounded to one decimal place).

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
SELECT ROUND(SUM(CASE WHEN c.call_category IN ('','n/a') OR c.call_category IS NULL THEN 1 ELSE 0 END)::DECIMAL
              / COUNT(policy_holder_id) * 100,1) AS uncategorize_call_pct 
FROM callers AS c;
```


**Solution Output**

| **uncategorize_call_pct** |
|:-------------------------:|
| 5.5                       |

