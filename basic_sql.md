- Write a query that displays all the data in the occurred_at, account_id, and channel columns of the web_events table, and limits the output to only the first 15 rows.
```sql
SELECT occurred_at, account_id, channel
FROM web_events
LIMIT 15;
```
