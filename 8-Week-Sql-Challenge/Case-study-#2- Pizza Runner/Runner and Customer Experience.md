QUESTION 1
-----------

How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```sql
WITH runner_registration AS (
    SELECT DATE_TRUNC('week', registration_date) AS week_start
    FROM runners
)
SELECT week_start, COUNT(*) AS num_of_registrations
FROM runner_registration
GROUP BY week_start
ORDER BY week_start;
```

**Results**

| week_number | num_of_registrations |
| ----------- | -------------------- |
| 1           | 2                    |
| 2           | 1                    |
| 3           | 1                    |

