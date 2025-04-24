- Find the total number of times each type of **channel** from the **web_events** was used. Your final table should have two columns - the **channel** and the number of times the channel was used.
```sql
SELECT channel, COUNT(*)
FROM web_events
GROUP BY channel;
```
