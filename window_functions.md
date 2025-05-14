Here we will treat the concept of **Window Functions** alongside its associated concepts like `OVER`, `PARTITION BY`, `ROW BETWEEN`, `RANK`, etc.

- Let us calculate the running total of `standard_qty` orders, agrregating them by each month.
```sql
SELECT standard_qty,
	DATE_TRUNC('month', occurred_at) AS month,
	SUM(standard_qty) OVER (PARTITION BY DATE_TRUNC('month', occurred_at) 
		ORDER BY occurred_at) AS running_total
FROM orders
```
