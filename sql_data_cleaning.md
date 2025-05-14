Here we will try out some examples for SQL aggregation concepts such as `LEFT`, `RIGHT`, `LENGTH`, `POSITION`, `STRPOS`, `SUBSTR`, `CONCAT`, `CAST` and `COALESCE` functions.  
### RIGHT()
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
SELECT name,
	primary_poc,
	LEFT(primary_poc, POSITION(' ' IN primary_poc) -1) AS fname,
	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) AS lname,
	CONCAT(LOWER(LEFT(primary_poc, POSITION(' ' IN primary_poc) -1)),'.',
		LOWER(RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' '))),
		'@',LOWER(REPLACE(name, ' ', '')),'.com') AS new_mail
FROM accounts;
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
- We would also like to create an initial password, which they will change after their first log in. The first password will be the first letter of the `primary_poc's` first name (lowercase), then the last letter of their first name (lowercase), the first letter of their last name (lowercase), the last letter of their last name (lowercase), the number of letters in their first name, the number of letters in their last name, and then the name of the company they are working with, all capitalized with no spaces.
```sql
WITH t1 AS (
    SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1 ) first_name,
	RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name, name
    FROM accounts)
SELECT first_name, last_name, 
	CONCAT(first_name, '.', last_name, '@', name, '.com'), LEFT(LOWER(first_name), 1) 
	|| RIGHT(LOWER(first_name), 1) || LEFT(LOWER(last_name), 1) || RIGHT(LOWER(last_name), 1) 
	|| LENGTH(first_name) || LENGTH(last_name) || REPLACE(UPPER(name), ' ', '')
FROM t1;
```
### DIVERSION
For this set of questions, we will be working with a single table. This is a different dataset than `Parch & Posey`, It is the `sf_crime_data` dataset, unfortunately we couldn't download it from source.
- Write a query to look at the top 10 rows to understand the columns and the raw data in the dataset.
```sql
SELECT *
FROM sf_crime_data
LIMIT 10;
```
**NOTE:** The format of the date column is mm/dd/yyyy with times that are not correct also at the end of the date. 
- Now let's write a query to change the date into the correct SQL date format `yyyy-mm-dd`. 
```sql
SELECT date original_date, 
       (SUBSTR(date,7,4)|| '-' ||
       LEFT(date,2)|| '-' ||
       SUBSTR(date,4,2)) AS new_date
FROM sf_crime_data;
```
- Now let us use either `CAST` or `::` to convert this to a date:
```sql
SELECT date original_date, 
       (SUBSTR(date,7,4)|| '-' ||
       LEFT(date,2)|| '-' ||
       SUBSTR(date,4,2))::DATE AS new_date
FROM sf_crime_data;
```
or
```sql
SELECT date original_date, 
       CAST(
         (SUBSTR(date,7,4)|| '-' ||
          LEFT(date,2)|| '-' ||
          SUBSTR(date,4,2)
         )AS DATE) AS new_date
FROM sf_crime_data;
```
This new date can be now operated on using DATE_TRUNC and DATE_PART.
### COALESCE()
- Write a query to find the row with missing data in the `orders` table:
```sql
SELECT *
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id
WHERE o.total IS NULL;
```
- Use **COALESCE** to fill in the `accounts.id` column with the `accounts.id` for the NULL value for the above table:
```sql
SELECT a.*,
	COALESCE(o.id, a.id) filled_id,
	o.*
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id
WHERE o.total IS NULL;
```
- Use **COALESCE** to fill in the `orders.account_id` column with the `account_id` for the NULL value for the above table:
```sql
SELECT a.*,
	COALESCE(o.account_id, a.id) account_id,
	o.occurred_at, 
	o.standard_qty, 
	o.gloss_qty, 
	o.poster_qty, 
	o.total, 
	o.standard_amt_usd, 
	o.gloss_amt_usd, 
	o.poster_amt_usd, 
	o.total_amt_usd
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id
WHERE o.total IS NULL;
```
- Use **COALESCE** to fill in each of the `qty` and `usd` columns with 0 for the table above:
```sql
SELECT a.*,
	COALESCE(o.account_id, a.id) account_id,
	o.occurred_at, 
	COALESCE(o.standard_qty, 0) standard_qty, 
	COALESCE(o.gloss_qty, 0) gloss_qty, 
	COALESCE(o.poster_qty, 0) poster_qty, 
	COALESCE(o.total, 0) total, 
	COALESCE(o.standard_amt_usd, 0) standard_amt_usd, 
	COALESCE(o.gloss_amt_usd, 0) gloss_amt_usd, 
	COALESCE(o.poster_amt_usd, 0) poster_amt_usd, 
	COALESCE(o.total_amt_usd, 0) total_amt_usd
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id
WHERE o.total IS NULL;
```
- Let's count the number of `id`'s in the original table:
```sql
SELECT COUNT(*) total_ids
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id;
```
**Note:** The total count of `id`'s is *6913*
- Now let's count the run the full `COALESCE` query without the 'WHERE' clause:
```sql
SELECT a.*,
	COALESCE(o.account_id, a.id) account_id,
	o.occurred_at, 
	COALESCE(o.standard_qty, 0) standard_qty, 
	COALESCE(o.gloss_qty, 0) gloss_qty, 
	COALESCE(o.poster_qty, 0) poster_qty, 
	COALESCE(o.total, 0) total, 
	COALESCE(o.standard_amt_usd, 0) standard_amt_usd, 
	COALESCE(o.gloss_amt_usd, 0) gloss_amt_usd, 
	COALESCE(o.poster_amt_usd, 0) poster_amt_usd, 
	COALESCE(o.total_amt_usd, 0) total_amt_usd
FROM accounts a
LEFT JOIN orders o
ON a.id = o.account_id;
```
