### Patient Support Analysis (Part 4) [UnitedHealth SQL Interview Question]

[Back to list of hard questions](../README.md)

<a href="https://datalemur.com/questions/long-calls-growth">Read the original question page</a>

UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

A long-call is categorised as any call that lasts more than 5 minutes (300 seconds). What's the month-over-month growth of long-calls?

Output the year, month (both in numerical and chronological order) and growth percentage rounded to 1 decimal place.


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
| 50986511             | b274-c8f0-4d5c-8704 |                   | 2022-01-28T09:46:00 | 252                    | 456                |
| 54026568             | 405a-b9be-45c2-b311 | n/a               | 2022-01-29T16:19:00 | 397                    | 217                |
| 54026568             | c4cc-fd40-4780-8a53 | benefits          | 2022-01-30T08:18:00 | 320                    | 134                |
| 54026568             | 81e8-6abf-425b-add2 | n/a               | 2022-02-20T17:26:00 | 1324                   | 83                 |
| 54475101             | 5919-b9c2-49a5-8091 |                   | 2022-02-24T18:07:00 | 206                    | 498                |
| 54624612             | a17f-a415-4727-9a3f | benefits          | 2022-02-27T10:56:00 | 435                    | 19                 |
| 53777383             | dfa9-e5a7-4a9b-a756 | benefits          | 2022-03-19T00:10:00 | 318                    | 69                 |
| 52880317             | cf00-56c4-4e76-963a | claims            | 2022-03-21T01:12:00 | 340                    | 254                |
| 52680969             | 0c3c-7b87-489a-9857 |                   | 2022-03-21T14:00:00 | 310                    | 213                |
| 54574775             | ca73-bf99-46b2-a79b | billing           | 2022-04-18T14:09:00 | 181                    | 312                |
| 51435044             | 6546-61b4-4a05-9a5e |                   | 2022-04-18T21:58:00 | 354                    | 439                |
| 52780643             | e35a-a7c2-4718-a65d | n/a               | 2022-05-06T14:31:00 | 318                    | 186                |
| 54026568             | 61ac-eee7-42fa-a674 |                   | 2022-05-07T01:27:00 | 404                    | 341                |
| 54674449             | 3d9d-e6e2-49d5-a1a0 | billing           | 2022-05-09T11:00:00 | 107                    | 450                |
| 54026568             | c516-0063-4b8f-aa74 |                   | 2022-05-13T01:06:00 | 404                    | 270                |

**Example Output**:

| **yr** | **mth** | **growth_pct** |
|--------|---------|----------------|
| 2022   | 1       | 0              |
| 2022   | 2       | 0              |
| 2022   | 3       | 50.0           |
| 2022   | 4       | -66.7          |
| 2022   | 5       | 200.0          |

**Explanation**

Call counts: Jan - 2 calls; Feb - 2 calls; Mar - 3 calls; Apr - 1 call; May - 3 calls

- In January, the percentage is 0% as there is no previous month's call info.
- In February, the percentage is 0% as there is no increase/decrease from January.
- In March, the growth is +50.0% as there is an increase of 1 call from February.
- In April, drop by 66.7% as there is a reduction of 2 calls from March.
- Finally, in May, there were 3 calls thus a 200.0% growth from April.


The dataset you are querying against may have different input & output - **this is just an example**!


**Solution**

```sql
WITH cte AS
(
  SELECT to_char(call_received, 'YYYY') AS yr,
    EXTRACT(MONTH FROM call_received) AS mth,
    COUNT(policy_holder_id) AS total
  FROM callers
  WHERE call_duration_secs >= 300
  GROUP BY to_char(call_received, 'YYYY'), 
    EXTRACT(MONTH FROM call_received)
  ORDER BY yr, mth
),
next_cte AS
(
  SELECT yr, 
    mth,
    total AS currentval,
    LAG(total) OVER() AS prevval
  FROM cte 
)
SELECT yr,
  mth,
  ROUND((currentval-prevval)::DECIMAL/prevval * 100,1) AS pct
FROM next_cte;
```


**Solution Output**

| **yr** | **mth** | **pct** |
|:------:|:-------:|:-------:|
| 2022   | 1       |         |
| 2022   | 2       | 0.0     |
| 2022   | 3       | 400.0   |
| 2022   | 4       | -50.0   |
| 2022   | 5       | 280.0   |
| 2022   | 6       | 15.8    |
| 2022   | 7       | -13.6   |
| 2022   | 8       | -26.3   |
| 2022   | 9       | -71.4   |
| 2022   | 10      | 25.0    |
| 2022   | 11      | 120.0   |
| 2022   | 12      | -36.4   |