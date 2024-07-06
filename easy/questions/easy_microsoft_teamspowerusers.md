### Teams Power Users [Microsoft SQL Interview Question]

[Back to list of easy questions](../README.md)


<a href="https://datalemur.com/questions/teams-power-users">Read the original question page</a>

Write a query to find the top 2 power users who sent the most messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending count of the messages.

Assumption:

- No two users has sent the same number of messages in August 2022.



`messages` **Table**:

| **Column Name** | **Type** |
|-----------------|----------|
| message_id      | integer  |
| sender_id       | integer  |
| receiver_id     | integer  |
| content         | varchar  |
| sent_date       | datetime |

`messages` **Example Input**:

| **message_id** | **sender_id** | **receiver_id** | **content**             | **sent_date**       |
|----------------|---------------|-----------------|-------------------------|---------------------|
| 901            | 3601          | 4500            | You up?                 | 08/03/2022 00:00:00 |
| 902            | 4500          | 3601            | Only if you're buying   | 08/03/2022 00:00:00 |
| 743            | 3601          | 8752            | Let's take this offline | 06/14/2022 00:00:00 |
| 922            | 3601          | 4500            | Get on the call         | 08/10/2022 00:00:00 |

**Example Output**:

| **sender_id** | **message_count** |
|---------------|-------------------|
| 3601          | 2                 |
| 4500          | 1                 |

The dataset you are querying against may have different input & output - **this is just an example**!

**Solution**

```sql
WITH messages_per_user AS
(
SELECT sender_id,
    COUNT(message_id) AS total_messages
FROM messages
WHERE sent_date BETWEEN '2022-08-01' AND '2022-08-31'
GROUP BY sender_id
)
SELECT sender_id,
    total_messages
FROM messages_per_user
ORDER BY total_messages DESC
LIMIT 2;
```


**Solution Output**


| **sender_id** | **total_messages** |
|:-------------:|:------------------:|
| 3601          | 4                  |
| 2520          | 3                  |