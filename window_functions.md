Here we will treat the concept of **Window Functions** alongside its associated concepts like `OVER`, `PARTITION BY`, `ROW_NUMBER`, `RANK` and `DENSE_RANK`.

- Let us calculate the running total of `standard_qty` orders, agrregating them by each month.
```sql
SELECT standard_qty,
	DATE_TRUNC('month', occurred_at) AS month,
	SUM(standard_qty) OVER (PARTITION BY DATE_TRUNC('month', occurred_at) 
		ORDER BY occurred_at) AS running_total
FROM orders;
```
- Let's create another running total of `standard_amt_usd` (in the `orders` table) over order time with no date truncation. The final table should have two columns: one with the amount being added for each new row, and a second with the running total.
```sql
SELECT standard_amt_usd,
	SUM(standard_amt_usd) OVER (ORDER BY occurred_at) AS running_total
FROM orders;
```
- Let's create a running total of `standard_amt_usd` (in the `orders` table) over order time, but this time, date truncate `occurred_at` by year and partition by that same year-truncated `occurred_at` variable. Your final table should have three columns: One with the amount being added for each row, one for the truncated date, and a final column with the running total within each year.
```sql
SELECT standard_amt_usd,
	DATE_TRUNC('year', occurred_at) AS year,
	SUM(standard_amt_usd) OVER (PARTITION BY DATE_TRUNC('year', occurred_at) 
		ORDER BY occurred_at) AS running_total
FROM orders;
```
- Let's rank the total number of paper ordered by account:
```sql
SELECT id,
       account_id,
       total,
       RANK() OVER (PARTITION BY account_id ORDER BY total DESC) AS total_rank
FROM orders;
```
### AGGREGATES IN WINDOW FUNCTIONS
- Let's experiment on aggregates in Window Functions with `ORDER BY`:
```sql
SELECT id,
       account_id,
       standard_qty,
       DATE_TRUNC('month', occurred_at) AS month,
       DENSE_RANK() OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS dense_rank,
       SUM(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS sum_std_qty,
       COUNT(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS count_std_qty,
       AVG(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS avg_std_qty,
       MIN(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS min_std_qty,
       MAX(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS max_std_qty
FROM orders;
```
- Now let's experiment on aggregates in Window Functions without `ORDER BY`:
```sql
SELECT id,
       account_id,
       standard_qty,
       DATE_TRUNC('month', occurred_at) AS month,
       DENSE_RANK() OVER (PARTITION BY account_id ) AS dense_rank,
       SUM(standard_qty) OVER (PARTITION BY account_id) AS sum_std_qty,
       COUNT(standard_qty) OVER (PARTITION BY account_id) AS count_std_qty,
       AVG(standard_qty) OVER (PARTITION BY account_id) AS avg_std_qty,
       MIN(standard_qty) OVER (PARTITION BY account_id) AS min_std_qty,
       MAX(standard_qty) OVER (PARTITION BY account_id) AS max_std_qty
FROM orders
```
**NOTE:** With ORDER BY, Ranks are assigned within each account_id partition based on month while without ORDER BY, all rows get rank 1 within their partition since there's no ordering criteria. Also, ORDER BY performs a running calculation on the aggregate calculations, while without ORDER BY there is a static partition calculation.
### Aliases for Multiple Window Functions
- Let's rewrite the first query with `WINDOW` aliases:
```sql
SELECT 
    id,
    account_id,
    standard_qty,
    DATE_TRUNC('month', occurred_at) AS month,
    DENSE_RANK() OVER account_window AS dense_rank,
    SUM(standard_qty) OVER account_window AS sum_std_qty,
    COUNT(standard_qty) OVER account_window AS count_std_qty,
    AVG(standard_qty) OVER account_window AS avg_std_qty,
    MIN(standard_qty) OVER account_window AS min_std_qty,
    MAX(standard_qty) OVER account_window AS max_std_qty
FROM orders
WINDOW account_window AS (PARTITION BY account_id ORDER BY DATE_TRUNC('month', occurred_at))
```
### Comparing Rows
```sql
SELECT account_id,
       standard_sum,
       LAG(standard_sum) OVER (ORDER BY standard_sum) AS lag,
       LEAD(standard_sum) OVER (ORDER BY standard_sum) AS lead,
       standard_sum - LAG(standard_sum) OVER (ORDER BY standard_sum) AS lag_difference,
       LEAD(standard_sum) OVER (ORDER BY standard_sum) - standard_sum AS lead_difference
FROM (
SELECT account_id,
       SUM(standard_qty) AS standard_sum
  FROM orders 
 GROUP BY 1
 ) sub
```
- Let us determine how the current order's total revenue ("total" meaning from sales of all types of paper) compares to the next order's total revenue.
