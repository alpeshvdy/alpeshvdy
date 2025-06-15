
# SQL Notes


- [SQL Notes](#sql-notes)
  - [Expression vs Statement](#expression-vs-statement)
  - [Filtering](#filtering)
    - [Using Logical Operators](#using-logical-operators)
    - [AND, OR, NOT](#and-or-not)
    - [NOT BETWEEN](#not-between)
    - [Another Way To Avoid Multiple ORs](#another-way-to-avoid-multiple-ors)
    - [LIKE](#like)
  - [ORDER BY](#order-by)
  - [LIMIT and OFFSET](#limit-and-offset)
  - [Aggregate Functions](#aggregate-functions)
    - [GROUP BY](#group-by)
    - [HAVING](#having)
    - [DISTINCT](#distinct)
  - [Math Functions](#math-functions)
    - [ABS](#abs)
    - [ROUND](#round)
    - [CEIL (Rounds up) , FLOOR (Rounds down)](#ceil-rounds-up--floor-rounds-down)
    - [POWER()](#power)
    - [MOD() or % - remainder of division between two numbers.](#mod-or----remainder-of-division-between-two-numbers)
    - [Divide Integers using CAST()](#divide-integers-using-cast)
    - [Calculate Percentages](#calculate-percentages)
  - [SQL NULL](#sql-null)
    - [Refining Data with SQL COALESCE()](#refining-data-with-sql-coalesce)
  - [CASE](#case)
  - [JOINS](#joins)
  - [Date-Time Functions](#date-time-functions)
  - [CTE vs. SUBQUERY](#cte-vs-subquery)
    - [CTEs](#ctes)
    - [SubQueries](#subqueries)
  - [Window Functions](#window-functions)
    - [Ranking](#ranking)
    - [LEAD \& LAG](#lead--lag)
    - [UNION](#union)
    - [INTERSECT](#intersect)
    - [EXCEPT](#except)
  - [Order of Execution](#order-of-execution)
  - [STRING Functions](#string-functions)


## Expression vs Statement

| Feature | Expression | Statement |
|--------|------------|-----------|
| Produces value? | ✅ Yes | ❌ No |
| Can it be assigned? | ✅ Yes (`x = 2 + 3`) | ❌ No (`x = if condition`) |
| Example | `2 + 2`, `"a" + "b"` | `if`, `for`, `while`, `def` |

## Filtering

### Using Logical Operators
The `WHERE` clause is often used with other logical operators like = (equals) and != (not equals) to filter down rows. Here's a table of all the logical operators you can use:
| Operator | Definition | Example in Query | Interpretation |
|--------|------------|-----------|-----------|
| = | Equals to | `field_name = 2` | field_name is equal to 2 |
| !=, <> | Not equals to | `field_name != 5` | field_name is not equal to 5 |
| <, > | Less than, more than | `field_name < 5, field_name > 10` | field_name is less than 5, field_name is more than 10 |
| <=, >= | Less than or equal to, more than or equal to | `field_name <= 5, field_name >= 2` | field_name is equal to or less than 5, field_name is equal to or more than 2 |

### AND, OR, NOT

Curious then why we'd use `NOT`, if `!=` could get us equivalent results? 👇👇

### NOT BETWEEN
While `=` has a ready-to-go negative complement of `!=`, there's many filtering SQL keywords that don't have a complement, so to negate the condition, you use the keyword `NOT`.
For example, if you wanted to find all Amazon reviews where the star count is NOT between 2 and 4 stars, here's the SQL query you'd use:

```sql 
SELECT * FROM reviews WHERE stars NOT BETWEEN 2 AND 4;
```

- `WHERE price > 10000 AND price <= 20000` means the total price is more than $10,000, but less than or equal to $20,000.
- `WHERE price BETWEEN 10000 AND 20000` is inclusive which means the total price is more than or equal to $10,000, but less than or equal to $20,000.

You can use SQL's `AND` operator with additional `AND` statements or any other comparison operator, as many times as you want.

### Another Way To Avoid Multiple ORs
In the next tutorial, we'll cover another way to filter on multiple conditions, while avoiding multiple OR statements, thanks to the SQL keyword `IN`.
```sql
SELECT drug, manufacturer, units_sold 
FROM pharmacy_sales
WHERE manufacturer IN ('Biogen', 'Bayer', 'Eli Lilly');
```

### LIKE

What if you could filter based on if your value matched some arbitrary PATTERN, instead of just a fixed list of values?
```sql
SELECT ...
FROM ...
WHERE column LIKE ... 
  AND/OR column NOT LIKE ...;

—--------
SELECT product_id, 
       manufacturer, 
       drug
FROM pharmacy_sales
WHERE drug LIKE '%Relief%';
```

| Example in Query | Definition |
|------------------|------------|
| `WHERE first_name LIKE 'a%'` | Finds any values that starts with "a" |
| `WHERE first_name LIKE '%a'` | Finds any values that ends with "a" |
| `WHERE first_name LIKE '%ae%'` | Finds any values that have "ae" in the middle |
| `WHERE first_name LIKE '_b%'` | Finds any values with "b" in the second position |
| `WHERE first_name LIKE 'a%o'` | Finds any values that starts with "a" and ends with "o" |
| `WHERE first_name LIKE 'a___'` | Finds any value that starts with "a" and has 3 characters |


## ORDER BY

```sql
SELECT column1, column2
FROM table_name
WHERE condition(s)
ORDER BY column1 ASC, column2 DESC; — Default is ASC
```


## LIMIT and OFFSET
```sql
SELECT * 
FROM callers
ORDER BY call_received DESC
OFFSET 10 —- skip the first 10 result
LIMIT 5; —- limit the output to first 5 records
```

## Aggregate Functions
Here's the 5 SQL Aggregate Functions – memorize them by heart:
- `SUM` adds together all the values in a particular column.
- `MIN` returns the lowest value in a particular column
- `MAX` returns the highest value in a particular column
- `AVG` calculates the average of a group of selected values.
- `COUNT` counts how many rows are in a particular column.

### GROUP BY
```sql
SELECT 
    category, 
    SUM(spend)
FROM product_spend
GROUP BY category;
—------
SELECT 
	ticker, 
    EXTRACT(YEAR FROM date) AS year, 
    ROUND(AVG(open),2) AS avg_open
FROM stock_prices 
GROUP BY ticker, year
ORDER BY year DESC;
```

### HAVING
```sql 
SELECT ticker, AVG(open), MIN(open)
FROM stock_prices
GROUP BY ticker
HAVING AVG(open) > 200 AND MIN(open) > 100;
```



| | WHERE | HAVING |
|----------|------------|---------------|
| When It Filters | Values BEFORE Grouping | Values AFTER Grouping |
| Operates On Data From | Individual Rows | Aggregated Values from Groups of Rows |
| Example | `SELECT username, followers FROM instagram_data WHERE followers > 1000;` | `SELECT country FROM instagram_data GROUP BY country HAVING AVG(followers) > 100;` |



### DISTINCT 
If you include two (or more) columns in a SELECT DISTINCT clause, your results will contain all of the unique pairs of those two columns.
```sql
SELECT DISTINCT user_id, status
FROM trades
ORDER BY user_id;
```

Output
| user_id | status |
|--------|---------|
| 111 | Completed |
| 111 | Cancelled |
| 148 | Completed |
| 178 | Completed |

Common use:
```sql
SELECT COUNT(DISTINCT user_id) 
FROM trades;
```

## Math Functions
### ABS
```sql  
SELECT 
  date, 
  ticker,
  (close-open) AS difference,
  ABS(close-open) AS abs_difference
FROM stock_prices
WHERE EXTRACT(YEAR FROM date) = 2023
  AND ticker = 'GOOG';
```
### ROUND
it rounds a number to a specified number of decimal places. 
```sql
SELECT 
  ticker,
  AVG(close) AS avg_close,
  ROUND(AVG(close), 2) AS rounded_avg_close
FROM stock_prices
WHERE EXTRACT(YEAR FROM date) = 2022
GROUP BY ticker;
```
### CEIL (Rounds up) , FLOOR (Rounds down)
```sql
SELECT 
  date,
  ticker,
  high,
  CEIL(high) AS resistance_level,
  low,
  FLOOR(low) AS support_level
FROM stock_prices
WHERE ticker = 'META'
ORDER BY date;
```
### POWER()
```sql
SELECT 
  date,
  ticker,
  close,
  ROUND(POWER(close, 2),2) AS squared_close
FROM stock_prices
WHERE ticker IN ('AAPL', 'GOOG', 'MSFT')
ORDER BY date;
```
### MOD() or % - remainder of division between two numbers.
```sql
SELECT 
  ticker,
  close,
  MOD(close, 5) AS price_remainder_mod,
  close%5 AS price_remainder_modulo
FROM stock_prices
WHERE ticker = 'GOOG';
```

### Divide Integers using CAST()
```sql
SELECT 
  CAST(10 AS DECIMAL)/4,
  CAST(10 AS FLOAT)/4,
  10/CAST(6 AS DECIMAL),
  10/CAST(6 AS FLOAT);

-- Trick:
SELECT 
  10/6,
  10*1.0/6,
  10/6*1.0,
  10/(6*1.0);
```

### Calculate Percentages
```sql
SELECT 
  sale_id,
  actual_sales,
  target_sales,
  ROUND((actual_sales / target_sales) * 100 ,2) AS sales_percentage_rounded
FROM sales;
```

## SQL NULL
- `IS NULL` and `IS NOT NULL`: Used to identify null and non-null values.
- `COALESCE()`: Returns the first non-null value from a list of arguments.
- `IFNULL()`: Substitutes null value with a specified value specified.

```sql
SELECT *
FROM goodreads
WHERE book_title = NULL;
—-----
SELECT *
FROM goodreads
WHERE book_title IS NULL;
—-----
SELECT *
FROM goodreads
WHERE book_title IS NOT NULL;

-- Customizing NULL Behaviour while sorting:
ORDER BY age ASC NULLS LAST;
ORDER BY age DESC NULLS FIRST;
```

### Refining Data with SQL COALESCE()
The `COALESCE()` function takes multiple inputs and returns the first non-null value.
`COALESCE(arg1, arg2, arg3, ...)`

IFNULL() function: Handles two arguments, returning the second if the first is null; else, it returns the first.
`IFNULL(expression, value_if_null)`

## CASE 
Using `CASE` statement in `SELECT` statement
```sql
SELECT
  character,
  superhero_alias,
  platform,
  CASE
    WHEN followers >= 700000 THEN 'Highly Popular'
    WHEN followers BETWEEN 300000 AND 699999 THEN 'Moderately Popular'
    ELSE 'Less Popular'  -- ELSE is optional, it can END
  END AS popularity_category
FROM marvel_avengers;
```

Using `CASE` statement in `WHERE` clause
```sql
SELECT 
  actor, 
  character, 
  platform
FROM marvel_avengers
WHERE 
  CASE 
    WHEN platform = 'Instagram' THEN followers >= 500000
    WHEN platform = 'Twitter' THEN followers >= 200000
    ELSE followers >= 100000
  END;
  ```

Counting results using SQL `COUNT()` in `CASE` statement
```sql
SELECT
  platform,
  COUNT(CASE 
    WHEN followers >= 500000 THEN 1
    ELSE NULL
  END) AS popular_actor_count,
  COUNT(CASE 
    WHEN followers < 500000 THEN 1
    ELSE NULL
  END) AS less_popular_actor_count
FROM marvel_avengers
GROUP BY platform;
```

Adding results using SQL `SUM()` in `CASE` statement
```sql
SELECT
  platform,
  SUM(CASE 
    WHEN engagement_rate >= 8.0 THEN followers
    ELSE 0
  END) AS high_engagement_followers_sum,
  SUM(CASE 
    WHEN engagement_rate < 8.0 THEN followers
    ELSE 0
  END) AS low_engagement_followers_sum
FROM marvel_avengers
GROUP BY platform;
```


Averaging results using SQL `AVG()` in `CASE` statement
```sql
SELECT
  platform,
  AVG(CASE 
    WHEN engagement_rate >= 8.0 THEN followers
    ELSE NULL
  END) AS avg_high_engagement_followers,
  AVG(CASE 
    WHEN engagement_rate < 8.0 THEN followers
    ELSE NULL
  END) AS avg_low_engagement_followers
FROM marvel_avengers
GROUP BY platform;
```

## JOINS
Syntex
```sql
SELECT *
FROM artists
[INNER/LEFT/RIGHT/FULL OUTER] JOIN songs
  ON artists.artist_id = songs.artist_id AND — if any other join condition
WHERE exp.. —- if any
```

Summary:

| Type of Joins | Description |
|---------------|-------------|
| INNER JOIN | Returns only the rows with matching values from both tables. |
| LEFT JOIN | Returns all the rows from the left table and the matching rows from the right table. |
| RIGHT JOIN | Returns all the rows from the right table and the matching rows from the left table. |
| FULL OUTER JOIN | Returns all rows when there is a match in either the left or the right table. If there is no match, NULL values are returned for columns from the table without a match. |

![image](https://github.com/user-attachments/assets/6243046c-9b25-44a3-b234-6d228cd75364)

## Date-Time Functions
`CURRENT_DATE`, `CURRENT_TIME` and `CURRENT TIMESTAMP` to return current date, time and timestamp.
```sql
SELECT 
  message_id,
  sent_date,
  CURRENT_DATE AS current_date,
  CURRENT_TIME AS current_time,
  CURRENT_TIMESTAMP AS current_timestamp
FROM messages
LIMIT 3;
```
Comparison operators <, >, =, <=, and >= to compare dates
```sql
SELECT *
FROM messages
WHERE sent_date >= '2022-08-10 00:00:00';
```

`EXTRACT()` and `DATE_PART()` to extract specific components of date.
```sql
SELECT 
  message_id, 
  sent_date,
  EXTRACT(YEAR FROM sent_date) AS extracted_year,
  DATE_PART('year', sent_date) AS part_year,
  EXTRACT(MONTH FROM sent_date) AS extracted_month,
  DATE_PART('month', sent_date) AS part_month,
  EXTRACT(DAY FROM sent_date) AS extracted_day,
  DATE_PART('day', sent_date) AS part_day,
  EXTRACT(HOUR FROM sent_date) AS extracted_hour,
  DATE_PART('hour', sent_date) AS part_hour,
  EXTRACT(MINUTE FROM sent_date) AS extracted_minute,
  DATE_PART('minute', sent_date) AS part_minute
FROM messages
LIMIT 3;
```

`DATE_TRUNC()` to round down date or timestamp into specific levels of precision.
```sql
SELECT 
  message_id,
  sent_date,
  DATE_TRUNC('month', sent_date) AS truncated_to_month, -- rounds to 1st day of the month (**/01/****)
  DATE_TRUNC('day', sent_date) AS truncated_to_day, -- rounds to 00:00:00
  DATE_TRUNC('hour', sent_date) AS truncated_to_hour -- rounds to the hr (**:00:00)
FROM messages
LIMIT 3;
```

`INTERVAL` to add or subtract time intervals in calculations.
```sql
SELECT 
  message_id,
  sent_date,
  sent_date + INTERVAL '2 days' AS add_2days,
  sent_date - INTERVAL '3 days' AS minus_3days,
  sent_date + INTERVAL '2 hours' AS add_2hours,
  sent_date - INTERVAL '10 minutes' AS minus_10mins
FROM messages
LIMIT 3;
```

`TO_CHAR()` to convert date or timestamp into strings.

| Format Name | Format | Example |
|-------------|--------|---------|
| ISO 8601 Date and Time | 'YYYY-MM-DD HH24:MI:SS' | '2023-08-27 14:30:00' |
| Date and Time with 12-hour Format | 'YYYY-MM-DD HH:MI:SS AM' | '2023-08-27 02:30:00 PM' |
| Long Month Name, Day and Year | 'Month DDth, YYYY' | 'August 27th, 2023' |
| Short Month Name, Day and Year | 'Mon DD, YYYY' | 'Aug 27, 2023' |
| Day, Month, and Year | 'DD Month YYYY' | '27 August 2023' |
| Day of the Month | 'Month' | 'August' |
| Day of the Week | 'Day' | 'Saturday' |

```sql
SELECT 
  message_id,
  sent_date,
  TO_CHAR(sent_date, 'YYYY-MM-DD HH:MI:SS') AS formatted_iso8601,
  TO_CHAR(sent_date, 'YYYY-MM-DD HH:MI:SS AM') AS formatted_12hr,
  TO_CHAR(sent_date, 'Month DDth, YYYY') AS formatted_longmonth,
  TO_CHAR(sent_date, 'Mon DD, YYYY') AS formatted_shortmonth,
  TO_CHAR(sent_date, 'DD Month YYYY') AS formatted_daymonthyear,
  TO_CHAR(sent_date, 'Month') AS formatted_dayofmonth,
  TO_CHAR(sent_date, 'Day') AS formatted_dayofweek
FROM messages
LIMIT 3;
```

`::DATE`, `TO_DATE()`, `::TIMESTAMP`, and `TO_TIMESTAMP()` to convert strings into date or timestamp.
```sql
SELECT 
  sent_date,
  sent_date::DATE AS casted_date,
  TO_DATE('2023-08-27', 'YYYY-MM-DD') AS converted_to_date,
  sent_date::TIMESTAMP AS casted_timestamp,
  TO_TIMESTAMP('2023-08-27 10:30:00', 'YYYY-MM-DD HH:MI:SS') AS converted_to_timestamp
FROM messages
LIMIT 3;
```

Output:

| sent_date | casted_date | converted_to_date | casted_timestamp | converted_to_timestamp |
|-----------|-------------|-------------------|------------------|------------------------|
| 08/03/2022 16:43:00 | 08/03/2022 00:00:00 | 08/27/2023 00:00:00 | 08/03/2022 16:43:00 | 08/27/2023 10:30:00 |
| 06/14/2022 14:30:00 | 06/14/2022 00:00:00 | 08/27/2023 00:00:00 | 06/14/2022 14:30:00 | 08/27/2023 10:30:00 |
| 08/12/2022 08:45:00 | 08/12/2022 00:00:00 | 08/27/2023 00:00:00 | 08/12/2022 08:45:00 | 08/27/2023 10:30:00 |


## CTE vs. SUBQUERY

### CTEs

- Break down complex queries  
    Use CTEs to simplify and organize complex logic into readable, modular parts.

    ```sql
    WITH cte_name AS (
      SELECT ...
      FROM ...
      WHERE ...
    )
    SELECT ...
    FROM cte_name
    JOIN ...
    ```

- Reuse query results
    Use CTEs to avoid repeating the same subquery multiple times in one query.

    ```sql
    WITH reusable_cte AS (
      SELECT ...
      FROM ...
    )
    SELECT ...
    FROM reusable_cte
    WHERE ...;
    ```

- Recursive queries
    Use CTEs when querying hierarchical or self-referencing data (e.g., org charts).

    ```sql
    WITH recursive_cte AS (
      -- Anchor member
      SELECT ...
      FROM ...
      WHERE ...

      UNION ALL

      -- Recursive member
      SELECT ...
      FROM table AS t
      JOIN recursive_cte AS r ON t.parent_id = r.id
    )
    SELECT * FROM recursive_cte;
    ```




### SubQueries

- Single-value filtering
    Use subqueries in WHERE to compare a value against an aggregate or computed result.
    ```sql
    SELECT ...
    FROM ...
    WHERE column > (
      SELECT AVG(column)
      FROM ...
    );
    ```

- Create dynamic computed columns
    Use subqueries in SELECT to add real-time calculations to each row.
    ```sql
    SELECT 
      column1,
      (SELECT MAX(value) FROM ...) AS max_value
    FROM ...;
    ```

- Filtering with IN, NOT IN, EXISTS
    Use subqueries to filter results based on membership in another query's result set.
    ```sql
    SELECT ...
    FROM ...
    WHERE id IN (
      SELECT id FROM ... WHERE ...
    );
    
    -- Using EXISTS
    SELECT ...
    FROM t1
    WHERE EXISTS (
      SELECT 1 FROM t2 WHERE t1.id = t2.id
    );
    ```

- Correlated subqueries
    Use when you need row-by-row comparisons between outer and inner queries (dependent subqueries).
    ```sql
    SELECT ...
    FROM table1 AS t1
    WHERE column = (
      SELECT MAX(column)
      FROM table2 AS t2
      WHERE t1.group_id = t2.group_id
    );
    ```


## Window Functions
```sql
SELECT
  spend,
   SUM(spend) OVER (
     PARTITION BY product —- can be partitioned by 2 columns
     ORDER BY transaction_date) AS running_total —- imp for rolling/moving metric
  FROM product_spend;
```

`COUNT()` counts the number of rows in a specified column across a defined window.
`SUM()` computes the sum of values within a specified column across a defined window.
`AVG()` calculates the average of a selected group of values across a defined window.
`MIN()` retrieves the lowest value from a particular column across a defined window.
`MAX()` fetches the highest value from a specific column across a defined window.
`FIRST_VALUE()` returns the first value in a designated column across a defined window.
`LAST_VALUE()` provides the last value in a given column across a defined window.

```sql
SELECT 
  category, 
  product, 
  user_id, 
  spend, 
  transaction_date,
  FIRST_VALUE(product) OVER (
    PARTITION BY user_id ORDER BY transaction_date) AS first_user_purchase,
  LAST_VALUE(product) OVER (
    PARTITION BY user_id ORDER BY transaction_date) AS last_user_purchase
FROM product_spend;
```


### Ranking
```sql 
SELECT 
  RANK() / DENSE_RANK() / ROW_NUMBER() OVER ( -- Compulsory expression
    PARTITION BY partitioning_expression -- Optional expression
    ORDER BY order_expression) -- Compulsory expression
FROM table_name;
```

- `ROW_NUMBER()`: This function assigns a unique sequential number to each row within a window. It's like numbering the rows in order.
- `RANK()` This function handles tied values by assigning the same rank to them. However, it may skip subsequent ranks, leaving gaps in the sequence.
- `DENSE_RANK()`: Similar to `RANK()`, `DENSE_RANK()` also handles tied values by assigning the same rank. However, it does not skip ranks, resulting in no gaps in the sequence.

```sql
SELECT 
  artist_name, 
  country,
  year_of_formation,
  ROW_NUMBER() OVER (PARTITION BY country ORDER BY year_of_formation) AS row_num,
 RANK() OVER (PARTITION BY country ORDER BY year_of_formation) AS rank_num,
 DENSE_RANK() OVER (PARTITION BY country ORDER BY year_of_formation) AS dense_rank_num
FROM concerts;
```

### LEAD & LAG 
`LEAD()` and `LAG()` are time-series window functions used to access data from rows that come after, or before the current row within a result set based on a specific column order.

```sql
LEAD(column_name, offset) OVER (  -- Compulsory expression
  PARTITION BY partition_column -- Optional expression
  ORDER BY order_column) AS …-- Compulsory expression
  
LAG(column_name, offset) OVER ( -- Compulsory expression
  PARTITION BY partition_column -- Optional expression
  ORDER BY order_column) AS …-- Compulsory expression
```

### UNION
`UNION ALL` keeps everything, including duplicates.
```sql
SELECT ingredient FROM recipe_1 UNION ALL
SELECT ingredient FROM recipe_2;
```
`UNION` removes duplicates, so you get only the unique items.
```sql
SELECT ingredient FROM recipe_1 UNION
SELECT ingredient FROM recipe_2;
```
### INTERSECT 
`INTERSECT` identifies common rows between two or more results.
```sql
SELECT ingredient FROM recipe_1 INTERSECT
SELECT ingredient FROM recipe_2;
```

### EXCEPT 
`EXCEPT` finds unique rows in the first `SELECT` query, but not present in the second `SELECT` query.
```sql
SELECT ingredient FROM recipe_1 EXCEPT
SELECT ingredient FROM recipe_2;
```

## Order of Execution

| Clause | Order | Description |
|--------|-------|-------------|
| FROM | 1 | The query begins with the FROM clause, where the database identifies the tables involved and accesses the necessary data. |
| WHERE | 2 | The database applies the conditions specified in the WHERE clause to filter the data retrieved from the tables in the FROM clause. |
| GROUP BY | 3 | If a GROUP BY clause is present, the data is grouped based on the specified columns, and aggregation functions (such as SUM(), AVG(), COUNT()) are applied to each group. |
| HAVING | 4 | The HAVING clause filters the aggregated data based on specified conditions. |
| SELECT | 5 | The SELECT clause defines the columns to be included in the final result set. |
| ORDER BY | 6 | If an ORDER BY clause is used, the result set is sorted according to the specified columns. |
| LIMIT/OFFSET | 7 |If LIMIT or OFFSET clause is present, the result set is restricted to the specified number of rows and optionally offset by a certain number of rows. |


## STRING Functions
- Tidying up data with UPPER() and LOWER()
- Extracting substrings with LEFT() and RIGHT()
    ```sql
    LEFT(string, n)
    RIGHT(string, n)
    ```
- Calculating length with LENGTH()
- Determining position with POSITION()
- Removing spaces using TRIM(), LTRIM(), RTRIM(), and BTRIM()
  `BTRIM()` —-Removes specified character from the start and end of the string.
- Concatenating strings with CONCAT()
- Concatenating strings with separators using CONCAT_WS()
- Slicing and dicing strings using SUBSTRING()
    `SUBSTRING(string, start_position [mandatory], length [optional])`
    ```sql
    SELECT 
      SUBSTRING('Spider-Man', 1, 6) AS substring_1, -- positive index
      SUBSTRING('Black Widow', 7) AS substring_2, -- positive index
      SUBSTRING('Spider-Man', -1) AS substring_3, -- negative index
      SUBSTRING('Black Widow', -2, 3) AS substring_4 -- output: ‘dow’ 
    FROM marvel_avengers;
    ```
- Extracting substring based on delimiter using SPLIT_PART()
    `SPLIT_PART(string, delimiter, part_number)`
    ```sql
    SELECT 
      SPLIT_PART('Spider-Man', '-', 1) AS split_part_1, -- first part
      SPLIT_PART('Black Widow', ' ', 2) AS split_part_2, --second part
      SPLIT_PART('Spider-Man', '-', -1) AS split_part_3, --last part
    FROM marvel_avengers;
    ```
