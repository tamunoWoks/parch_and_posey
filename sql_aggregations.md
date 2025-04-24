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
- Lets's hard code the **MEDIAN**
```sql
SELECT *
FROM (SELECT total_amt_usd
         FROM orders
         ORDER BY total_amt_usd
         LIMIT 3457) AS Table1
ORDER BY total_amt_usd DESC
LIMIT 2;
```
**Note:** This is more advanced than the topics we have covered thus far to build a general solution. The above used a SUBQUERY, but you could use any method to find the two necessary values
- Which **account** (by name) placed the earliest order? Your solution should have the **account name** and the **date** of the order.
```sql
SELECT a.name, o.occurred_at
FROM accounts a
JOIN orders o
ON a.id = o.account_id
ORDER BY o.occurred_at
LIMIT 1;
```
- Find the total sales in **usd** for each account. You should include two columns - the total sales for each company's orders in **usd** and the company **name**.
```sql
SELECT a.name, SUM(o.total_amt_usd) total_sales
FROM accounts a
JOIN orders o
ON a.id = o.account_id
GROUP BY a.name;
```
- Via what **channel** did the most recent (latest) **web_event** occur, which **account** was associated with this **web_event**? Your query should return only three values - the **date**, **channel**, and **account name**.
```sql
SELECT a.name, w.channel, w.occurred_at
FROM accounts a
JOIN web_events w
ON a.id = w.account_id
ORDER BY w.occurred_at DESC
LIMIT 1;
```
- Who was the **primary contact** associated with the earliest **web_event**?
```sql
SELECT primary_poc
FROM accounts a
JOIN web_events w
ON a.id = w.account_id
ORDER BY w.occurred_at 
LIMIT 1;
```
- What was the smallest order placed by each **account** in terms of total usd. Provide only two columns - the **account name** and the total usd. Order from smallest dollar amounts to largest.
```sql
SELECT a.name, MIN(o.total_amt_usd) min_order
FROM accounts a
JOIN orders o
On a.id = o.account_id
GROUP BY a.name
ORDER BY min_order;
```
**Note:** Sort of strange we have a bunch of orders with no dollars. We might want to look into those.
- Find the number of **sales reps** in each region. Your final table should have two columns the **region** and the number of **sales_reps**. Order from fewest reps to most reps.
```sql
SELECT r.name, COUNT(*) num_reps
FROM region r
JOIN sales_reps s
ON r.id = s.region_id
GROUP BY r.name
ORDER BY num_reps;
```
- For each account, determine the average amount of each type of paper they purchased across their orders. Your result should have four columns - one for the account **name** and one for the average quantity purchased for each of the paper types for each account.
```sql
SELECT a.name,
       AVG(o.standard_qty) avg_std,
       AVG(o.gloss_qty) avg_gloss,
       AVG(o.poster_qty) avg_post
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.name;
```
- For each account, determine the average amount spent per order on each paper type. Your result should have four columns - one for the account **name** and one for the average amount spent on each paper type.
```sql
SELECT a.name,
       AVG(o.standard_amt_usd) avg_std_amt,
       AVG(o.gloss_amt_usd) avg_gloss_amt,
       AVG(o.poster_amt_usd) avg_post_amt
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.name;
```
- Determine the number of times a particular **channel** was used in the **web_events** table for each **sales rep**. Your final table should have three columns - the **name of the sales rep**, the **channel**, and the number of occurrences. Order your table with the highest number of occurrences first.
```sql
SELECT s.name, w.channel,
       COUNT(*) AS num_events
FROM accounts a
JOIN sales_reps s ON a.sales_rep_id = s.id
JOIN web_events w ON w.account_id = a.id
GROUP BY s.name, w.channel
ORDER BY num_events DESC;
```
