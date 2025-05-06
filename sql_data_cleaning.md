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
- Use the **accounts** table and a `CASE` statement to create two groups: one group of company names that start with a number and a second group of those company names that start with a letter. What proportion of company names start with a letter?
```sql
SELECT SUM(num) nums, SUM(letter) letters
FROM (SELECT name, CASE WHEN LEFT(UPPER(name), 1) IN ('0','1','2','3','4','5','6','7','8','9') 
                          THEN 1 ELSE 0 END AS num, 
            CASE WHEN LEFT(UPPER(name), 1) IN ('0','1','2','3','4','5','6','7','8','9') 
                          THEN 0 ELSE 1 END AS letter
         FROM accounts) t1;
```  
There are 350 company names that start with a letter and 1 that starts with a number. This gives a ratio of 350/351 that are company names that start with a letter or 99.7%.
