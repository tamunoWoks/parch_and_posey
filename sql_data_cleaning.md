Here we will try out some examples for SQL aggregation concepts such as `LEFT`, `RIGHT`, `LENGTH`, `POSITION`, `STRPOS`, `SUBSTR`, CONCAT`, `CAST` and `COALESCE` functions.  

- In the **accounts** table, there is a column holding the **website** for each company. The last three digits specify what type of web address they are using. Pull these extensions and provide how many of each website type exist in the **accounts** table.
```sql
SELECT RIGHT(website, 3) as domain,
	COUNT(*) as subscribers
FROM accounts
GROUP BY 1
ORDER BY 2 DESC;
```
- Use the **accounts** table to pull the first letter of each company name to see the distribution of company names that begin with each letter (or number).
```sql
SELECT LEFT(UPPER(name), 1) as initial,
	COUNT(*) as no_of_coy
FROM accounts
GROUP BY 1
ORDER BY 2 DESC;
```
