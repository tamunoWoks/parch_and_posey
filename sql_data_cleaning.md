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
- Consider vowels as `a`, `e`, `i`, `o`, and `u`. What proportion of company names start with a vowel, and what percent start with anything else?
```sql
SELECT SUM(vowel) vowels, SUM(other) others
FROM (SELECT name, CASE WHEN LEFT(UPPER(name), 1) IN ('A','E','I','O','U') 
                          THEN 1 ELSE 0 END AS vowel, 
            CASE WHEN LEFT(UPPER(name), 1) IN ('A','E','I','O','U') 
                          THEN 0 ELSE 1 END AS other
         FROM accounts) t1;
```
There are 80 company names that start with a vowel and 271 that start with other characters. Therefore 80/351 are vowels or 22.8%. Therefore, 77.2% of company names do not start with vowels.
- Use the `accounts` table to create **first** and **last** name columns that hold the first and last names for the `primary_poc`.
```sql
SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 ) first_name, 
   	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name
FROM accounts;
```
We can also do this with `POSITION()`
```sql
SELECT LEFT(primary_poc, POSITION(' ' IN primary_poc) -1) first_name,
	RIGHT(primary_poc, LENGTH(name) - POSITION(' ' IN primary_poc)) last_name
FROM accounts
```
- Let us do the same thing for every rep `name` in the `sales_reps` table.
```sql
SELECT LEFT(name, STRPOS(name, ' ') -1 ) first_name, 
   	RIGHT(name, LENGTH(name) - STRPOS(name, ' ')) last_name
FROM sales_reps;
```
We can also do this with `POSITION()`
```sql
SELECT LEFT(name, POSITION(' ' IN name) -1) first_name,
	RIGHT(name, LENGTH(name) - POSITION(' ' IN name)) last_name
FROM sales_reps
```
- Each company in the `accounts` table wants to create an email address for each `primary_poc`. The email address should be the first name of the **primary_poc** `.` last name **primary_poc** `@` company name `.com`.
```sql
SELECT name,
	primary_poc,
	LEFT(primary_poc, POSITION(' ' IN primary_poc) -1) AS fname,
	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) AS lname,
	LOWER(LEFT(primary_poc, POSITION(' ' IN primary_poc) -1))||'.'
	||LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')))
	||'@'||LOWER(name)||'.com' AS new_mail
FROM accounts;
```
or
```sql
SELECT name,
	primary_poc,
	LEFT(primary_poc, POSITION(' ' IN primary_poc) -1) AS fname,
	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) AS lname,
	CONCAT(LOWER(LEFT(primary_poc, POSITION(' ' IN primary_poc) -1)),'.',
		LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' '))),
		'@',LOWER(name),'.com') AS new_mail
FROM accounts;
```
or
```sql
WITH t1 AS (
    SELECT LOWER(LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 )) first_name,
		LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' '))) last_name,
		name
    FROM accounts)
SELECT first_name, last_name, CONCAT(first_name, '.', last_name, '@',LOWER(name), '.com')
FROM t1;
```
- You may have noticed that in the previous solution some of the company names include spaces, which will certainly not work in an email address. Let's see if we can create an email address that will work by removing all of the spaces in the account name.
```sql
SELECT name,
	primary_poc,
	LEFT(primary_poc, POSITION(' ' IN primary_poc) -1) AS fname,
	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) AS lname,
	LOWER(LEFT(primary_poc, POSITION(' ' IN primary_poc) -1))||'.'
	||LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')))
	||'@'||LOWER(REPLACE(name, ' ', ''))||'.com' AS new_mail
FROM accounts;
```
or
```sql

```
or
```sql
WITH t1 AS (
    SELECT LOWER(LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 )) first_name,
		LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' '))) last_name,
		name
    FROM accounts)
SELECT first_name, last_name, CONCAT(first_name, '.', last_name, '@', REPLACE(LOWER(name), ' ', ''), '.com')
FROM  t1;
```
