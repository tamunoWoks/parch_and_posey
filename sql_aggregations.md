- Find the total number of times each type of **channel** from the **web_events** was used. Your final table should have two columns - the **channel** and the number of times the channel was used.
```sql
SELECT channel, COUNT(*)
FROM web_events
GROUP BY channel;
```
- Find the total amount of **poster_qty** paper ordered in the **orders** table.
```sql
SELECT SUM(poster_qty) AS total_poster_sales
FROM orders;
```
- Find the total amount of **standard_qty** paper ordered in the **order** table.
```sql
SELECT SUM(standard_qty) AS total_standard_sales
FROM orders;
```
- Find the total dollar amount of sales using the **total_amt_usd** in the **orders** table.
```sql
SELECT SUM(total_amt_usd) AS total_dollar_sales
FROM orders;
```
- Find the total amount for each individual order that was spent on **standard** and **gloss** paper in the orders table. This should give a dollar amount for each order in the table.
```sql
SELECT standard_amt_usd + gloss_amt_usd AS total_standard_gloss
FROM orders;
```
**Note:** This solution did not use an aggregate.
