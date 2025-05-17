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
