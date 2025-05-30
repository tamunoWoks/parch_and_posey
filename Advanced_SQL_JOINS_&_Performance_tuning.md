- Let us find each account who has a sales rep and each sales rep that has an account:
```sql
SELECT *
FROM accounts
FULL JOIN sales_reps ON accounts.sales_rep_id = sales_reps.id;
```
- Let us also check each account that does not have a sales rep and each sales rep that does not have an account:
```sql
SELECT *
FROM accounts
FULL JOIN sales_reps ON accounts.sales_rep_id = sales_reps.id
WHERE accounts.sales_rep_id IS NULL OR sales_reps.id IS NULL;
```
**NOTE:** Unmatched rows do not exist for this query.
### Inequality JOINs
- Let us retrieve the order data along with all `web events` that occurred before each order, but only for `orders` placed in the very first month of order records:
```sql
SELECT o.id AS order_id,
        o.occurred_at AS order_date,
	e.*
FROM orders o
LEFT JOIN web_events AS e
	ON e.account_id = o.account_id
	AND e.occurred_at < o.occurred_at
WHERE DATE_TRUNC('month', o.occurred_at) = (
			SELECT DATE_TRUNC('month', MIN(occurred_at))
			FROM orders
			)
ORDER BY o.account_id, o.occurred_at;
```
- Let us write a query that left joins the `accounts` table and the `sales_reps` tables on each sale rep's ID number and joins it using the `<` comparison operator on `accounts.primary_poc` and `sales_reps.name`:
```sql
SELECT accounts.name as account_name,
       accounts.primary_poc as poc_name,
       sales_reps.name as sales_rep_name
FROM accounts
LEFT JOIN sales_reps
    ON accounts.sales_rep_id = sales_reps.id
   AND accounts.primary_poc < sales_reps.name;
```
### SELF JOINs
- Let us identify the pairs of orders from the same account where the second order occurred within 28 days after the first order. 
```sql
SELECT o1.id AS o1_id,
       o1.account_id AS o1_account_id,
       o1.occurred_at AS o1_occurred_at,
       o2.id AS o2_id,
       o2.account_id AS o2_account_id,
       o2.occurred_at AS o2_occurred_at
FROM orders o1
LEFT JOIN orders o2
   ON o1.account_id = o2.account_id
  AND o2.occurred_at > o1.occurred_at
  AND o2.occurred_at <= o1.occurred_at + INTERVAL '28 days'
ORDER BY o1.account_id, o1.occurred_at;
```
- Modify the previous query to perform the same interval analysis except for the `web_events` table. Also:  
	- Change the interval to 1 day to find those web events that occurred after, but not more than 1 day after, another web event.
	- Add a column for the channel variable in both instances of the table in your query
```sql
SELECT we1.id AS we_id,
       we1.account_id AS we1_account_id,
       we1.occurred_at AS we1_occurred_at,
       we1.channel AS we1_channel,
       we2.id AS we2_id,
       we2.account_id AS we2_account_id,
       we2.occurred_at AS we2_occurred_at,
       we2.channel AS we2_channel
FROM web_events we1 
LEFT JOIN web_events we2
   ON we1.account_id = we2.account_id
  AND we1.occurred_at > we2.occurred_at
  AND we1.occurred_at <= we2.occurred_at + INTERVAL '1 day'
ORDER BY we1.account_id, we2.occurred_at;
```
### UNION
#### Appending Data via UNION
- Write a query that uses UNION ALL on two instances (and selecting all columns) of the accounts table:
```sql
SELECT *
    FROM account
UNION ALL
SELECT *
  FROM accounts;
```
**NOTE:** This query returned 702 rows, however if we used `UNION` instead of `UNION ALL`, it would return 351 rows as `UNION` only returns distict rows.
#### Pretreating Tables before doing a UNION
- Add a WHERE clause to each of the tables that you unioned in the query above, filtering the first table where name equals Walmart and filtering the second table where name equals Disney:
```sql
SELECT *
    FROM accounts
    WHERE name = 'Walmart'
UNION ALL
SELECT *
  FROM accounts
  WHERE name = 'Disney';
```
- The results from the above query can also be generated with:
```sql
SELECT *
FROM accounts
WHERE name = 'Walmart' OR name = 'Disney';
```
#### Performing Operations on a Combined Dataset
- Perform the union in your first query (under the **Appending Data via UNION header**) in a `CTE` and name it `double_accounts`. Then do a `COUNT` the number of times a name appears in the `double_accounts` table:
```sql
WITH double_accounts AS (
    SELECT *
      FROM accounts
    UNION ALL
    SELECT *
      FROM accounts
)

SELECT name,
       COUNT(*) AS name_count
 FROM double_accounts 
GROUP BY 1
ORDER BY 2 DESC;
```
### JOINing Subqueries
- Write a query that joins the account, order, and web event tables to show which orders and web events occurred on the same day:
```sql
SELECT o.occurred_at AS date,
       a.sales_rep_id,
       o.id AS order_id,
       we.id AS web_event_id
FROM   accounts a
JOIN   orders o
ON     o.account_id = a.id
JOIN   web_events we
ON     DATE_TRUNC('day', we.occurred_at) = DATE_TRUNC('day', o.occurred_at)
ORDER BY 1 DESC;
```
**NOTE:** The result of the above query is very robust and has over `70,000` rows.
- We can get the same result much more efficiently by aggregating the tables seperately so the `COUNT` are performed across far smaller datasets. 
```sql
SELECT COALESCE(orders.date, web_events.date) AS date,
	orders.active_sales_reps,
	orders.orders,
	web_events.web_visits
FROM (
	SELECT DATE_TRUNC('day', o.occurred_at) AS date,
	       COUNT(a.sales_rep_id) AS active_sales_reps,
	       COUNT(o.id) AS orders
	FROM   accounts a
	JOIN   orders o
	ON     o.account_id = a.id
	GROUP BY 1
	) orders

FULL JOIN (
	SELECT DATE_TRUNC('day', we.occurred_at) AS date,
	       COUNT(we.id) AS web_visits
	FROM   web_events we
	GROUP BY 1
	) web_events
ON web_events.date = orders.date
ORDER BY 1 DESC;
```
**NOTE:**  This preaggregation returns `1,000 rows, and we use a full join just in case one table has observations the other doesn't have.
