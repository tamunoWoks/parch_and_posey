## SQL Aggregations
Here will will deal with SQL aggregation concepts such as `COUNT`, `SUM`, `MIN`, `MAX`, `AVG`, `NULL`, `GROUP BY`, `DISTINCT`, `HAVING`, `CASE` and `DATE` functions.  

**Examples**
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
- What are the different **channels** used by **account id** `1001`? Your final table should have only 2 columns: **account name** and the different **channels**. You can try **SELECT DISTINCT** to narrow down the results to only the unique values.
```sql
SELECT DISTINCT a.name, w.channel
FROM accounts a
JOIN web_events w
ON a.id = w.account_id
WHERE a.id = '1001';
```
- Use DISTINCT to test if there are any **accounts** associated with more than one **region**.
```sql
SELECT a.id as "account id", r.id as "region id", 
a.name as "account name", r.name as "region name"
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
JOIN region r ON r.id = s.region_id;

SELECT DISTINCT id, name
FROM accounts;
```
**Note:** The two queries have the same number of resulting rows (351), so we know that every account is associated with only one region. If each account was associated with more than one region, the first query should have returned more rows than the second query.
- Have any sales reps worked on more than one account?
```sql
SELECT s.id , s.name, COUNT(*) num_acc
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY s.id, s.name
ORDER BY num_acc;
```
**Note:** Actually all of the sales reps have worked on more than one account. The fewest number of accounts any sales rep works on is 3. There are 50 sales reps, and they all have more than one account.
```sql
SELECT DISTINCT id, name
FROM sales_reps;
```
**Note:** Using DISTINCT in the second query assures that all of the sales reps are accounted for in the first query.
- How many of the **sales reps** have more than 5 accounts that they manage?
```sql
SELECT s.id, s.name, COUNT(*) num_accounts
FROM accounts a
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY s.id, s.name
HAVING COUNT(*) > 5
ORDER BY num_accounts;
```
**Note:** Technically, we can get this using a **SUBQUERY** as shown below. This same logic can be used for the other queries.
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
- How many **accounts** have more than 20 orders?
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
**Note:** This query above only works if there are no ties for the account that used facebook the most. It is a best practice to use a larger limit number first such as 3 or 5 to see if there are ties before using LIMIT 1.
- Which channel was most frequently used by most accounts?
```sql
SELECT a.id, a.name, w.channel, COUNT(*) channel_use
FROM accounts a
JOIN web_events w ON a.id = w.account_id
GROUP BY a.id, a.name, w.channel
ORDER BY channel_use DESC
LIMIT 10;
```
**Note:** All of the top 10 are `direct`.
- Find the sales in terms of total dollars for all orders in each `year`, ordered from greatest to least. Do you notice any trends in the yearly sales totals?
```sql
SELECT DATE_PART('year', occurred_at) ord_year,  SUM(total_amt_usd) total_spent
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** When we look at the yearly totals, you might notice that `2013` and `2017` have much smaller totals than all other years. Sales have been increasing year over year, with 2016 being the largest sales to date. At this rate, we might expect 2017 to have the largest sales.
```sql
SELECT DATE_PART('year', occurred_at) ord_year, 
       DATE_PART('month', occurred_at) ord_mth,
       SUM(total_amt_usd) total
FROM orders
GROUP BY 1, 2
ORDER BY 1 DESC;
```
**Note:** If we look further at the monthly data, we see that for 2013 and 2017 there is only one month of sales for each of these years (12 for 2013 and 1 for 2017). Therefore, neither of these are evenly represented.
- Which **month** did Parch & Posey have the greatest sales in terms of total dollars? Are all months evenly represented by the dataset?
```sql
SELECT DATE_PART('month', occurred_at) ord_mth,
       SUM(total_amt_usd) total_amt
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** In order for this to be 'fair', we should remove the sales from 2013 and 2017. For the same reasons as discussed above.
```sql
SELECT DATE_PART('month', occurred_at) ord_mth,
       SUM(total_amt_usd) total_amt
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** The greatest sales amounts occur in December (12).
- Which **year** did Parch & Posey have the greatest sales in terms of total number of orders? Are all years evenly represented by the dataset?
```sql
SELECT DATE_PART('year', occurred_at) ord_yr,
       COUNT(*) total_sales
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** Again, 2016 by far has the most amount of orders, but again 2013 and 2017 are not evenly represented to the other years in the dataset.
- Which **month** did Parch & Posey have the greatest sales in terms of total number of orders? Are all months evenly represented by the dataset?
```sql
SELECT DATE_PART('month', occurred_at) ord_mth,
       COUNT(*) total_sales
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** December still has the most sales, but interestingly, November has the second most sales (but not the most dollar sales. To make a fair comparison from one month to another 2017 and 2013 data were removed.
- In which **month** of which **year** did `Walmart` spend the most on gloss paper in terms of dollars?
```sql
SELECT DATE_TRUNC('month', o.occurred_at) ord_date,
       SUM(gloss_amt_usd) tot_gloss_amt
FROM accounts a
JOIN orders o ON a.id = o.account_id
WHERE a.name = 'Walmart'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
- Create a column that divides the `standard_amt_usd` by the `standard_qty` to find the `unit price` for standard paper for each order. Limit the results to the first 10 orders, and include the `id` and `account_id` fields.
```sql
SELECT id, account_id, standard_amt_usd/standard_qty AS unit_price
FROM orders
```
**Note:** We were thrown an error for a division by zero. We can use a CASE statement to solve this. This way any time the standard_qty is zero, we will return 0, and otherwise we will return the unit_price.
```sql
SELECT account_id, CASE WHEN standard_qty = 0 OR standard_qty IS NULL THEN 0
                        ELSE standard_amt_usd/standard_qty END AS unit_price
FROM orders
```
**Note:** The first part of the statement will catch any of those division by zero values that were causing the error, and the other components will compute the division as necessary. You will notice, we essentially charge all of our accounts 4.99 for standard paper. It makes sense this doesn't fluctuate, and it is more accurate than adding 1 in the denominator like our quick fix earlier.
**Note:** May 2016 was when Walmart spent the most on gloss paper.
- Write a query to display for each order, the account ID, total amount of the order, and the level of the order - ‘Large’ or ’Small’ - depending on if the order is $3000 or more, or less than $3000.
```sql
SELECT account_id, total_amt_usd,
   CASE 
       WHEN total_amt_usd > 3000 THEN 'Large'
       ELSE 'Small' 
   END AS order_level
FROM orders;
```
- Write a query to display the number of orders in each of three categories, based on the `total` number of items in each order. The three categories are: 'At Least 2000', 'Between 1000 and 2000' and 'Less than 1000'.
```sql
SELECT
   CASE
      WHEN total >=2000 THEN 'At Least 2000'
      WHEN total >=1000 AND total < 2000 THEN 'Between 1000 and 2000'
      ELSE 'Less Than 1000' 
   END AS order_category,
   COUNT(*) AS order_count
FROM orders
GROUP BY 1;
```
- We would like to understand 3 different branches of customers based on the amount associated with their purchases. The top branch includes anyone with a Lifetime Value (total sales of all orders) `greater than 200,000` usd. The second branch is between `200,000 and 100,000` usd. The lowest branch is anyone `under 100,000` usd. Provide a table that includes the **level** associated with each **account**. You should provide the **account name**, the **total sales of all orders** for the customer, and the **level**. Order with the top spending customers listed first.
```sql
SELECT a.name, SUM(total_amt_usd) total_spent,
   CASE 
     WHEN SUM(total_amt_usd) > 200000 THEN 'High'
     WHEN SUM(total_amt_usd) <= 200000 AND SUM(total_amt_usd) >= 100000 THEN 'mid'
     ELSE 'low'
   END AS customer_level
FROM accounts a
JOIN orders o ON a.id = o.account_id
GROUP BY a.name
ORDER BY 2 DESC;
```
- We would now like to perform a similar calculation to the first, but we want to obtain the total amount spent by customers only in 2016 and 2017. Keep the same levels as in the previous question. Order with the top spending customers listed first.
```sql
SELECT a.name, SUM(total_amt_usd) total_spent,
   CASE 
     WHEN SUM(total_amt_usd) > 200000 THEN 'High'
     WHEN SUM(total_amt_usd) <= 200000 AND SUM(total_amt_usd) >= 100000 THEN 'mid'
     ELSE 'low'
   END AS customer_level
FROM accounts a
JOIN orders o ON a.id = o.account_id
WHERE occurred_at > '2015-12-31' 
GROUP BY a.name
ORDER BY 2 DESC;
```
- We would like to identify top performing sales reps, which are **sales reps** associated with more than 200 orders. Create a table with the **sales rep name**, the total number of orders, and a column with `top` or `not` depending on if they have more than 200 orders. Place the top sales people first in your final table.
```sql
SELECT s.name, COUNT(*) total_orders,
   CASE
     WHEN COUNT(*) > 200 THEN 'Top'
     ELSE 'Not top'
   END AS sales_rep_class
FROM orders o
JOIN accounts a ON a.id = o.account_id
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY 1
ORDER BY 2 DESC;
```
**Note:** It is worth mentioning that this assumes each name is unique - which has been done a few times. We otherwise would want to break by the name and the id of the table.
- The previous didn't account for the middle, nor the dollar amount associated with the sales. Management decides they want to see these characteristics represented as well. We would like to identify top performing **sales reps**, which are sales reps associated with more than `200` orders or more than `750000` in total sales. The `middle` group has any **rep** with more than `150` orders or `500000` in sales. Create a table with the **sales rep name**, the total number of orders, total sales across all orders, and a column with `top`, `middle`, or `low` depending on this criteria. Place the top sales people based on dollar amount of sales first in your final table.
```sql
SELECT s.name, COUNT(*), SUM(o.total_amt_usd) total_spent,
   CASE
     WHEN COUNT(*) > 200 OR SUM(o.total_amt_usd) > 750000 THEN 'Top'
     WHEN COUNT(*) > 150 OR SUM(o.total_amt_usd) > 500000 THEN 'Middle'
     ELSE 'low'
   END AS sales_rep_level
FROM orders o
JOIN accounts a ON a.id = o.account_id
JOIN sales_reps s ON s.id = a.sales_rep_id
GROUP BY 1
ORDER BY 3 DESC;
```
