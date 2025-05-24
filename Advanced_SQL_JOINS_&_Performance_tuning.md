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
- Let us retrieve the order data along with all `web events` that occurred before each order, but only for `orders` placed in the very first month of order records.
