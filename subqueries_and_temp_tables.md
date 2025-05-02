## Subqueries & Temp Tables
Here we will try out some examples that implement Subqueries & Temp tables in SQL alongside concepts like `WITH`.  

**Examples**
- Find the number of events that occur for each day for each channel
```sql
SELECT DATE_TRUNC('day',occurred_at) AS day,
       channel, COUNT(*) as events
FROM web_events
GROUP BY 1,2
ORDER BY 3 DESC;
```
- Create a subquery that simply provides all of the data from the previous query
```sql
SELECT *
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
                channel, COUNT(*) as events
          FROM web_events 
          GROUP BY 1,2
          ORDER BY 3 DESC) sub;
```
- Now find the average number of events for each channel per day.
```sql
SELECT channel, AVG(events) AS average_events
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
                channel, COUNT(*) as events
         FROM web_events 
         GROUP BY 1,2) sub
GROUP BY channel
ORDER BY 2 DESC;
```
- Use **DATE_TRUNC** to pull `month` level information about the first order ever placed in the **orders** table.
```sql
SELECT DATE_TRUNC('month', MIN(occurred_at)) 
FROM orders;
```
- Use the result from the previous query to find only the orders that took place in the same month and year as the first order.
```sql
SELECT AVG(standard_qty) avg_std,
       AVG(gloss_qty) avg_gls,
       AVG(poster_qty) avg_pst
FROM orders
WHERE DATE_TRUNC('month', occurred_at) = (SELECT DATE_TRUNC('month', MIN(occurred_at))
                                          FROM orders);
```
- Use the same result to pull the average for each type of paper `qty` in this month.
```sql
SELECT SUM(total_amt_usd)
FROM orders
WHERE DATE_TRUNC('month', occurred_at) = (SELECT DATE_TRUNC('month', MIN(occurred_at))
                                          FROM orders);
```
- Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales:

First, we to find the total_amt_usd totals associated with each sales rep, and the region in which they were located. The query below provided this information.  
```sql
SELECT s.name rep, r.name reg, SUM(o.total_amt_usd) as total
FROM sales_reps s 
JOIN accounts a ON a.sales_rep_id = s.id
JOIN orders o ON a.id = o.account_id
JOIN region r ON r.id = s.region_id
GROUP BY 1, 2
ORDER BY 3 DESC;
```  

Next, we the max for each region, and then we can use this to pull those rows in our final result.
```sql
SELECT reg, MAX(total) as total
 FROM(SELECT s.name rep, r.name reg, SUM(o.total_amt_usd) as total
      FROM sales_reps s 
      JOIN accounts a ON a.sales_rep_id = s.id
      JOIN orders o ON a.id = o.account_id
      JOIN region r ON r.id = s.region_id
      GROUP BY 1, 2) as t1
GROUP BY 1
ORDER BY 2 DESC;
```

To finalize, let us JOIN two tables, where the region and amount match.
```sql
SELECT t3.rep, t3.reg, t3.total
FROM (SELECT reg, MAX(total) as total
      FROM(SELECT s.name rep, r.name reg, SUM(o.total_amt_usd) as total
           FROM sales_reps s 
           JOIN accounts a ON a.sales_rep_id = s.id
           JOIN orders o ON a.id = o.account_id
           JOIN region r ON r.id = s.region_id
           GROUP BY 1, 2) as t1
      GROUP BY 1) as t2
JOIN (SELECT s.name rep, r.name reg, SUM(o.total_amt_usd) as total
      FROM sales_reps s 
      JOIN accounts a ON a.sales_rep_id = s.id
      JOIN orders o ON a.id = o.account_id
      JOIN region r ON r.id = s.region_id
      GROUP BY 1, 2
      ORDER BY 3 DESC) as t3
ON t3.reg = t2.reg AND t3.total = t2.total;
```
- For the region with the largest sales **total_amt_usd**, how many **total** orders were placed?

We will first write a query to pull the total_amt_usd for each region:  
```sql
SELECT r.name reg_name, SUM(o.total_amt_usd) total_sales
FROM sales_reps s 
JOIN region r ON r.id = s.region_id
JOIN accounts a ON s.id = a.sales_rep_id
JOIN orders o ON o.account_id = a.id
GROUP BY 1
ORDER BY 2 DESC;
```
Next let us retrieve the region with the max amount from this table. There are two ways this can be done:
1. One was to pull the max using a subquery.
```sql
SELECT MAX(total_sales)
	FROM (SELECT r.name reg_name, SUM(o.total_amt_usd) total_sales
			FROM sales_reps s 
			JOIN region r ON r.id = s.region_id
			JOIN accounts a ON s.id = a.sales_rep_id
			JOIN orders o ON o.account_id = a.id
			GROUP BY 1) AS sub
```
2. Another way is to order descending and just pull the top value.
```sql
SELECT r.name reg_name, SUM(o.total_amt_usd) total_sales
FROM sales_reps s 
JOIN region r ON r.id = s.region_id
JOIN accounts a ON s.id = a.sales_rep_id
JOIN orders o ON o.account_id = a.id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
Finally, we want to pull the total orders for the region with this amount:
```sql
SELECT r.name, COUNT(o.total) as total_orders
FROM sales_reps s 
JOIN accounts a ON s.id = a.sales_rep_id
JOIN orders o ON o.account_id = a.id
JOIN region r ON r.id = s.region_id
GROUP BY r.name
HAVING SUM(o.total_amt_usd) = (SELECT MAX(total_sales)
				FROM (SELECT r.name reg_name, SUM(o.total_amt_usd) total_sales
					FROM sales_reps s 
					JOIN region r ON r.id = s.region_id
					JOIN accounts a ON s.id = a.sales_rep_id
					JOIN orders o ON o.account_id = a.id
					GROUP BY 1) AS sub);
```
This provides the Northeast with 2357 orders.
- How many **accounts** had more **total** purchases than the account **name** which has bought the most **standard_qty** paper throughout their lifetime as a customer?  
