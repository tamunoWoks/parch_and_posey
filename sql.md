Here are a few queries used to understand the Parch & Posey database.
- Write a query to return the 10 earliest orders in the **orders** table. Include the `id`, `occurred_at`, and `total_amt_usd`.
```sql
SELECT id, occurred_at, total_amt_usd
FROM orders
ORDER BY occurred_at
LIMIT 10;
```
- Write a query to return the top 5 **orders** in terms of largest `total_amt_usd`. Include the `id`, `account_id`, and `total_amt_usd`.
```sql
SELECT id, account_id, total_amt_usd
FROM orders
ORDER BY total_amt_usd DESC 
LIMIT 5;
```
- Write a query to return the lowest 20 **orders** in terms of smallest `total_amt_usd`. Include the `id`, `account_id`, and `total_amt_usd`.
 ```sql
SELECT id, account_id, total_amt_usd
FROM orders
ORDER BY total_amt_usd
LIMIT 20;
```
- Write a query that displays the order ID, account ID, and total dollar amount for all the orders, sorted first by the account ID (in ascending order), and then by the total dollar amount (in descending order).
```sql
SELECT id, account_id, total_amt_usd
FROM orders
ORDER BY account_id, total_amt_usd DESC;
```
- Now write a query that again displays order ID, account ID, and total dollar amount for each order, but this time sorted first by total dollar amount (in descending order), and then by account ID (in ascending order).
```sql
SELECT id, account_id, total_amt_usd
FROM orders
ORDER BY total_amt_usd DESC, account_id;
```
- Pulls the first 5 rows and all columns from the orders table that have a dollar amount of gloss_amt_usd greater than or equal to 1000.
```sql
SELECT *
FROM orders
WHERE gloss_amt_usd >= 1000
LIMIT 5;
```
- Pulls the first 10 rows and all columns from the orders table that have a total_amt_usd less than 500.
```sql
SELECT *
FROM orders
WHERE total_amt_usd < 500
LIMIT 10;
```
- Filter the accounts table to include the company `name`,`website`, and the primary point of contact `(primary_poc`) just for the Exxon Mobil company in the **accounts** table.
```sql
SELECT name, website, primary_poc
FROM accounts
WHERE name != 'Exxon Mobil'
```
- Create a column that divides the `standard_amt_usd` by the `standard_qty` to find the unit price for standard paper for each order. Limit the results to the first 10 orders, and include the `id` and `account_id` fields.
```sql
SELECT id,
       account_id,
       standard_amt_usd,
       standard_qty,
       standard_amt_usd/standard_qty as unit_price
FROM orders
LIMIT 10;
```
- Use the accounts table to find all the companies whose names start with 'C'.
```sql
SELECT name
FROM accounts
WHERE name LIKE 'C%';
```
- Use the accounts table to find all companies whose names contain the string 'one' somewhere in the name.
```sql
SELECT name
FROM accounts
WHERE name LIKE '%one%';
```
- Use the accounts table to find all companies whose names end with 's'.
```sql
SELECT name
FROM accounts
WHERE name LIKE '%s';
```
- Use the **accounts** table to find the account `name`, `primary_poc`, and `sales_rep_id` for Walmart, Target, and Nordstrom.
```sql
SELECT name, primary_poc, sales_rep_id
FROM accounts
WHERE name IN ('Walmart','Target','Nordstrom');
```
- Use the **web_events** table to find all information regarding individuals who were contacted via the **channel** of `organic` or `adwords`.
```sql
SELECT *
FROM web_events
WHERE channel IN ('organic', 'adwords');
```
- Use the **accounts** table to find the account name, primary poc, and sales rep id for all stores except Walmart, Target, and Nordstrom.
```sql
SELECT name, primary_poc, sales_rep_id
FROM accounts
WHERE name NOT IN ('Walmart', 'Target', 'Nordstrom');
```
- Use the web_events table to find all information regarding individuals who were contacted via any method except using organic or adwords methods.
```sql
SELECT *
FROM web_events
WHERE channel NOT IN ('organic', 'adwords');
```
- Use the accounts table to find all the companies whose names do not start with 'C'.
```sql
SELECT *
FROM accounts
WHERE name NOT LIKE 'C%';
```
- Use the accounts table to find all companies whose names do not contain the string 'one' somewhere in the name.
```sql
SELECT *
FROM accounts
WHERE name NOT LIKE '%one%';
```
- Use the accounts table to find all companies whose names do not end with 's'.
```sql
SELECT *
FROM accounts 
WHERE name NOT LIKE '%s';
```
- Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for the 'Midwest' region. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to account name.
```sql
SELECT r.name region, s.name rep, a.name account
FROM sales_reps s
JOIN region r
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
WHERE r.name = 'Midwest'
ORDER BY a.name;
```
- Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for accounts where the sales rep has a first name starting with `S` and in the `Midwest region`. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to account name.
```sql
SELECT r.name region, s.name rep, a.name account
FROM sales_reps s
JOIN region r
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
WHERE r.name = 'Midwest' AND s.name LIKE 'S%'
ORDER BY a.name;
```
- Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for accounts where the sales rep has a **last** name starting with `K` and in the `Midwest` region. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to account name.
```sql
SELECT r.name region, s.name rep, a.name account
FROM sales_reps s
JOIN region r
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
WHERE r.name = 'Midwest' AND s.name LIKE '% K%'
ORDER BY a.name;
```
- Provide the **name** for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. However, you should only provide the results if the **standard order quantity** exceeds `100`. Your final table should have 3 columns: **region name**, **account name**, and **unit price**.
```sql
SELECT r.name region,
       a.name account,
       o.total_amt_usd/(o.total + 0.01) unit_price
FROM region r
JOIN sales_reps s
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
WHERE o.standard_qty > 100;
```
- Provide the **name** for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. However, you should only provide the results if the **standard order quantity** exceeds `100` and the **poster order quantity** exceeds `50`. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. Sort for the smallest **unit price** first.
```sql
SELECT r.name region,
       a.name account,
       o.total_amt_usd/(o.total + 0.01) unit_price
FROM region r
JOIN sales_reps s
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
WHERE o.standard_qty > 100 AND o.poster_qty > 50
ORDER BY unit_price;
```
- Provide the **name** for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. However, you should only provide the results if the **standard order quantity** exceeds `100` and the **poster order quantity** exceeds `50`. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. Sort for the largest **unit price** first.
```sql
SELECT r.name region,
       a.name account,
       o.total_amt_usd/(o.total + 0.01) unit_price
FROM region r
JOIN sales_reps s
ON s.region_id = r.id
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
WHERE o.standard_qty > 100 AND o.poster_qty > 50
ORDER BY unit_price DESC;
```
- Which **account** (by name) placed the earliest order? Your solution should have the **account name** and the **date** of the order.
```sql
SELECT a.name, o.occurred_at
FROM accounts a
JOIN orders o
ON a.id = o.account_id
ORDER BY o.occurred_at
LIMIT 1;
```
- Find the total sales in usd for each account. You should include two columns - the total sales for each company's orders in usd and the company **name**.
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
- Find the total number of times each type of **channel** from the **web_events** was used. Your final table should have two columns - the **channel** and the number of times the channel was used.
```sql
SELECT channel, COUNT(*)
FROM web_events
GROUP BY channel;
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
Sort of strange we have a bunch of orders with no dollars. We might want to look into those.
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
- Determine the number of times a particular **channel** was used in the **web_events** table for each **region**. Your final table should have three columns - the **region name**, the **channel**, and the number of occurrences. Order your table with the highest number of occurrences first.
```sql
SELECT r.name, w.channel,
       COUNT(*) AS num_events
FROM accounts a
JOIN web_events w
ON a.id = w.account_id
JOIN sales_reps s
ON s.id = a.sales_rep_id
JOIN region r
ON r.id = s.region_id
GROUP BY r.name, w.channel
ORDER BY num_events DESC;
```
- Use DISTINCT to test if there are any accounts associated with more than one region.
```sql
SELECT a.id as "account id", r.id as "region id", 
a.name as "account name", r.name as "region name"
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
JOIN region r ON r.id = s.region_id;

SELECT DISTINCT id, name
FROM accounts;
```
The two queries have the same number of resulting rows (351), so we know that every account is associated with only one region. If each account was associated with more than one region, the first query should have returned more rows than the second query.
- Have any sales reps worked on more than one account?
```sql
SELECT s.id , s.name, COUNT(*) num_acc
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY s.id, s.name
ORDER BY num_acc;
```
Actually all of the sales reps have worked on more than one account. The fewest number of accounts any sales rep works on is 3. There are 50 sales reps, and they all have more than one account.
```sql
SELECT DISTINCT id, name
FROM sales_reps;
```
Using DISTINCT in the second query assures that all of the sales reps are accounted for in the first query.
- How many of the sales reps have more than 5 accounts that they manage?
```sql
SELECT s.id, s.name, COUNT(*) num_accounts
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY s.id, s.name
HAVING COUNT(*) > 5
ORDER BY num_accounts;
```
Technically, we can get this using a **SUBQUERY** as shown below. This same logic can be used for the other queries.
```sql
SELECT COUNT(*) num_reps_above5
FROM(SELECT s.id, s.name, COUNT(*) num_accounts
        FROM accounts a
        JOIN sales_reps s
        ON s.id = a.sales_rep_id
        GROUP BY s.id, s.name
        HAVING COUNT(*) > 5
        ORDER BY num_accounts) AS Table1;
```
- How many accounts have more than 20 orders?
```sql
SELECT a.id, a.name, COUNT(*) num_orders
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
HAVING COUNT(*) > 20
ORDER BY num_orders;
```
- Which account has the most orders?
```sql
SELECT a.id, a.name, COUNT(*) most_orders
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY most_orders DESC
LIMIT 1;
```
- Which accounts spent more than 30,000 usd total across all orders?
```sql
SELECT a.id, a.name, SUM(o.total_amt_usd) as total
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
HAVING SUM(o.total_amt_usd) > 30000
ORDER BY total;
```
- Which accounts spent less than 1,000 usd total across all orders?
```sql
SELECT a.id, a.name, SUM(o.total_amt_usd) as total
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
HAVING SUM(o.total_amt_usd) < 1000
ORDER BY total;
```
- Which account has spent the most?
```sql
SELECT a.id, a.name, SUM(o.total_amt_usd) total
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY total DESC
LIMIT 1;
```
- Which account has spent the least?
```sql
SELECT a.id, a.name, SUM(o.total_amt_usd) total
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY total
LIMIT 1;
```
- Which accounts used `facebook` as a **channel** to contact customers more than 6 times?
```sql
SELECT a.id, a.name, w.channel, COUNT(*) channel_use
FROM accounts a
JOIN web_events w ON a.id = w.account_id
GROUP BY a.id, a.name, w.channel
HAVING w. channel = 'facebook' AND COUNT(*) > 6
ORDER BY channel_use;
```
- Which account used `facebook` most as a **channel**?
```sql
SELECT a.id, a.name, w.channel, COUNT(*) channel_use
FROM accounts a
JOIN web_events w On a.id = w.account_id
GROUP BY a.id, a.name, w.channel
HAVING w.channel = 'facebook'
ORDER BY channel_use DESC
LIMIT 1;
```
