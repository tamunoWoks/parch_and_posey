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
SELECT id, account_id, standard_amt_usd, standard_qty, standard_amt_usd/standard_qty as unit_price
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
SELECT r.name region, a.name account, o.total_amt_usd/(o.total + 0.01) unit_price
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
SELECT r.name region, a.name account, o.total_amt_usd/(o.total + 0.01) unit_price
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
SELECT r.name region, a.name account, o.total_amt_usd/(o.total + 0.01) unit_price
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
