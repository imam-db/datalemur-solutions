### Patient Support Analysis (Part 3) [UnitedHealth SQL Interview Question]

[Back to list of hard questions](../README.md)


<a href="https://datalemur.com/questions/patient-call-history">Read the original question page</a>

UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Write a query to get the patients who made a call within 7 days of their previous call. If a patient called more than twice in a span of 7 days, count them as once.

If you like this question, try out Patient Support Analysis (Part 4)!


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

| **policy_holder_id** | **case_id**         | **call_category** | **call_received** | **call_duration_secs** | **original_order** |
|----------------------|---------------------|-------------------|-------------------|------------------------|--------------------|
| 50837000             | dc63-acae-4f39-bb04 | claims            | 3/9/2022 2:51     | 205                    | 130                |
| 50837000             | 41be-bebe-4bd0-a1ba | IT_support        | 3/12/2022 5:37    | 254                    | 129                |
| 50837000             | bab1-3ec5-4867-90ae | benefits          | 5/13/2022 18:19   | 228                    | 339                |
| 50936674             | 12c8-b35c-48a3-b38d | claims            | 5/31/2022 7:27    | 240                    | 31                 |
| 50886837             | d0b4-8ea7-4b8c-aa8b | IT_support        | 3/11/2022 3:38    | 276                    | 16                 |
| 50886837             | a741-c279-41c0-90ba |                   | 3/19/2022 10:52   | 131                    | 325                |

**Example Output**:

| **patient_count** |
|-------------------|
| 1                 |

**Explanation**

Only patient 50837000 made another call (on March 12th, 2022) within 7 days of the last call (on March 9th, 2022).

The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH cte AS
(
SELECT policy_holder_id,
  call_received,
  LEAD(call_received) OVER (PARTITION BY policy_holder_id ORDER BY call_received ASC) AS next_call
FROM callers
)

SELECT COUNT(DISTINCT policy_holder_id) AS patient_count
FROM cte
WHERE EXTRACT(epoch FROM next_call-call_received) <= 7*3600*24;
```


**Solution Output**

| **patient_count** |
|-------------------|
| 40                |