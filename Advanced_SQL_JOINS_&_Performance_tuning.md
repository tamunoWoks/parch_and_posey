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
- Write a query that uses UNION ALL on two instances (and selecting all columns) of the accounts table.
```sql
SELECT *
    FROM account
UNION ALL
SELECT *
  FROM accounts
```
