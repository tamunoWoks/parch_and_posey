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
- Though the **price/standard_qty** paper varies from one order to the next. I would like this ratio across all of the sales made in the **orders** table.
```sql
SELECT SUM(standard_amt_usd)/SUM(standard_qty) AS standard_price_per_unit
FROM orders;
```
**Note:** This solution used both an aggregate and our mathematical operators
- When was the earliest order ever placed?
```sql
SELECT MIN(occurred_at) 
FROM orders;
```
- Try performing the same query without using an aggregation function.
```sql
SELECT occurred_at 
FROM orders 
ORDER BY occurred_at
LIMIT 1;
```
- When did the most recent (latest) **web_event** occur?
```sql
SELECT MAX(occurred_at)
FROM web_events;
```
- Let's perform the result of the previous query without using an aggregation function.
```sql
SELECT occurred_at
FROM web_events
ORDER BY occurred_at DESC
LIMIT 1;
```
- Find the mean (**AVERAGE**) amount spent per order on each paper type, as well as the mean amount of each paper type purchased per order. Your final answer should have 6 values - one for each paper type for the average number of sales, as well as the average amount.
```sql
SELECT AVG(standard_qty) mean_standard, AVG(gloss_qty) mean_gloss, 
              AVG(poster_qty) mean_poster, AVG(standard_amt_usd) mean_standard_usd, 
              AVG(gloss_amt_usd) mean_gloss_usd, AVG(poster_amt_usd) mean_poster_usd
FROM orders;
```
