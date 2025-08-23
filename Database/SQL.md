```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Sublanguages

|     |                                                                             |
| --- | --------------------------------------------------------------------------- |
| DDL | Data Definition Language. Defines data structure                            |
| DML | Data Manipulation Language. Insert, Update, Delete record                   |
| DCL | Data Control Language. Grant or revoke access permissons to database object |
| TCL | Transaction Control Language. Defines concurrent operation boundaries       |
| DQL | Data Query Language. Search, filter, group, aggregate stored data           |
- DDL
    - CREATE
	    - - Database
		- User
		- Table
		- Index
		- Trigger
		- Function
		- Stored Procedure
		- View
    - ALTER
	    - - Add/Drop columns
		- Add/Drop constraints
		- Modify column data types
		- Modify column constraints
    - DROP
    - TRUNCATE
    - RENAME
- DML
    - INSERT
    - UPDATE
    - DELETE
- DQL
    - SELECT
- DCL
    - GRANT
    - REVOKE
+ TCL
	+ COMMIT
	+ ROLLBACK
	+ SAVEPOINT.
# Constraints

| Constraint  | Use                                                                       |
| ----------- | ------------------------------------------------------------------------- |
| Not Null    | Ensures that a column's value is not null.                                |
| Unique      | Ensures that a column's value is unique in the table.                     |
| Primary Key | Combines `unique` and `not null`. Uniquely identifies each row.           |
| Foreign Key | Links to a row in another table. Prevents the destruction of those links. |
| Default     | Specifies a value for a column, if one is not given.                      |
| Check       | Ensures the value of a column satisfies a specific condition.             |
- - - **PostgreSQL** - `SERIAL` implicitly creates a sequence to generate unique values.
        - **MySQL** - `AUTO_INCREMENT` automatically increments the value for each new row.
        - **SQLite** - `AUTOINCREMENT` ensures that each new row inserted into the table will automatically receive a unique and incrementing value.

# Order of Execution
1. `FROM`, `JOIN`
2. `WHERE`
3. `GROUP BY`
4. aggregate functions
5. `HAVING`
6. **window functions**
7. `SELECT`
8. `DISTINCT`
9. `UNION`/`INTERSECT`/`EXCEPT`
10. `ORDER BY`
11. `OFFSET`
12. `LIMIT`/`FETCH`/`TOP`
# CTE, SubQuery and Correlated Subquery
## CTE
+ Common Table Expression (CTE) is a temporary table to store results.
+ Use when we have to reuse results of a subquery.
+ Use when performing recursive queries.
+ Makes query more readable.
+ CTE only exists for the duration of the main query
```sql
-- Start CTE
WITH genre_revenue_cte AS(
		SELECT genre,
			SUM(concert_revenue) AS total_revenue
		FROM concerts
		GROUP BY genre
)
-- End CTE

SELECT g.genre, 
	g.total_revenue,
	AVG(c.concert_revenue) AS avg_concert_revenue
FROM genre_revenue_cte g
INNER JOIN concerts c
	ON g.genre = c.genre
WHERE c.concert_revenue > g.total_revenue*0.5
GROUP BY g.genre, g.total_revenue;
```
## Subquery
+ Query within query
+ Temporary table within inner parenthesis. 
+ Cannot store and reuse result.
+ Inner query runs first, then main query
+ Use for single value comparison in `WHERE` clause.
```sql
SELECT artist_name 
FROM concerts 
WHERE concert_revenue > ( SELECT AVG(concert_revenue) FROM concerts);
```
+ Use to create new columns or aggregate results
```sql
SELECT artist_name, genre, concert_revenue, 
(SELECT AVG(concert_revenue) FROM concerts) AS avg_concert_revenue, (SELECT MAX(concert_revenue) FROM concerts) AS max_concert_revenue FROM concerts;
```
## Correlated Subqueries
+ Subquery which is processed once for each row in main query.
+ Inner query uses value from outer query making it dependent on outer query
```sql
SELECT last_name, salary, department_id  
 FROM employees outer  
 WHERE salary >  
                (SELECT AVG(salary)  
                 FROM employees  
                 WHERE department_id =  
                        outer.department_id group by department_id);
```

# Window Function
+ Used only in `SELECT` and `ORDER BY`.
```sql
-- Syntax
SELECT <column_1>, <column_2>,
  <window_function> OVER (
    PARTITION BY <...>
    ORDER BY <...>
    <window_frame>) <window_column_alias>
FROM <table_name>;

SELECT 
  city, 
  month, 
  SUM(sold) OVER (PARTITION BY city) AS sum
FROM sales;
```
+ Operates by creating **windows** (or partitions) in the dataset. `PARTITION BY` clause is used to create partitions. Without the clause, the entire result set is the partition.
![Example of PARTITION BY clause in window functions](https://learnsql.com/blog/sql-window-functions-cheat-sheet/partition-by.png)
+ `ORDER BY` clause defines the order of rows in each partition. Without the clause, the order of rows in a partition is arbitrary.
+ A **window frame** is a set of rows, related to **current row**. It is evaluated **separately** within each partition.![Bounds in window frame definition](https://learnsql.com/blog/sql-window-functions-cheat-sheet/window-frame.png)
```sql
-- Syntax for window frame
ROWS | RANGE | GROUPS BETWEEN lower_bound AND upper_bound
-- GROUPS only available in PostgreSQL 11+
```
Bounds can be one of five options:
	- `UNBOUNDED PRECEDING`
	- `n PRECEDING`
	- `CURRENT ROW`
	- `n FOLLOWING`
	- `UNBOUNDED FOLLOWING`

![Example for ROWS, RANGE, GROUPS clauses in window frame definition](https://learnsql.com/blog/sql-window-functions-cheat-sheet/rows-range-groups.png)
## When to Use
+ When you need to maintain granularity of data for in-depth analysis
+ For scenarios like cumulative sum, ranking, percentile.
+ When we want to use positional functions like `LEAD()` or `LAG()` i.e. functions that obtain column value from other records in the same window.
## Window Function vs Group By
```sql
-- Group by
SELECT Department, AVG(Salary) as Avg_Salary  
FROM employees  
GROUP BY Department;
```
![](https://miro.medium.com/v2/resize:fit:514/1*fxH-qwvhTQ1T3b--hf6APw.png)
```sql
-- Window
SELECT Department, Salary, AVG(Salary) 
OVER (PARTITION BY Department) AS Avg_Salary  
FROM employees;
```
![](https://miro.medium.com/v2/resize:fit:768/1*cvQ1UFeuKqzUPiRqHu1Xwg.png)

+ `GROUP BY` collapses the results of grouping into a single row, while **Window function** returns a value for each row, making it possible to gain deeper insights without collapsing data
## Ranking functions
+ ***DO NOT*** accept **window frame**
### `ROW_NUMBER()` 
+ Ranking starts with 1.
+ If rows have the same value, they all will get consecutive rankings i.e. if two rows have the same value, `ROW_NUMBER()` would place them at 5th and 6th.
+ Can't predict which row with same value is ranked higher.
+ Does not require `ORDER BY` clause.
### `RANK()` 
+ Creates a ranking based on providing column.
+ Ranking starts with 1.
+ If rows have same value, ranked the same, but next spot is adjusted (Think 1, 2, 2, 4).
+ Requires a `ORDER BY` clause.
### `DENSE_RANK()` 
+ Creates a ranking based on providing column.
+ Ranking starts with 1.
+ If rows have same value, ranked the same, **no adjustment**. (Think 1, 2, 2, 3)
+ Requires a `ORDER BY` clause.
```sql
SELECT
  RANK() OVER(ORDER BY points DESC) AS rank,
  DENSE_RANK() OVER(ORDER BY points DESC) AS dense_rank,
  ROW_NUMBER() OVER(ORDER BY points DESC) AS row_number,
  first_name,
  last_name,
  points
FROM exam_result;
```

| rank | dense_rank | row_number | first_name | last_name  | points |
| ---- | ---------- | ---------- | ---------- | ---------- | ------ |
| 1    | 1          | 1          | Marlene    | Duncan     | 90     |
| 1    | 1          | 2          | Rayhan     | Williamson | 90     |
| 1    | 1          | 3          | Marius     | Powell     | 90     |
| 1    | 1          | 4          | Eryk       | Myers      | 90     |
| 5    | 2          | 5          | Evie-May   | Boyer      | 80     |
| 6    | 3          | 6          | Emyr       | Downes     | 70     |
| 6    | 3          | 7          | Dina       | Morin      | 70     |
| 8    | 4          | 8          | Nora       | Parkinson  | 60     |
| 9    | 5          | 9          | Joanne     | Goddard    | 50     |
| 10   | 6          | 10         | Trystan    | Oconnor    | 40     |
## Distribution Functions
+ ***REQUIRE*** `ORDER BY` clause.
+ ***DO NOT*** accept **window frame**
- `percent_rank()` - the percentile ranking number of a row—a value in [0, 1] interval: (rank-1) / (total number of rows - 1)
- `cume_dist()` - the cumulative distribution of a value within a group of values, i.e., the number of rows with values less than or equal to the current row’s value divided by the total number of rows; a value in (0, 1] interval
![Example for window functions cume_dist(), percent_rank()](https://learnsql.com/blog/sql-window-functions-cheat-sheet/cume_dist-percent_rank.png)
## Analytic Functions
+ ***REQUIRE*** `ORDER BY` clause.
+ ***DO NOT*** accept **window frame** (lead, lag, ntile)
+ `lead(expr, offset, default)` - the value for the row _offset_ rows after the current; _offset_ and _default_ are optional; default values: _offset_ = 1, _default_ = `NULL`
- `lag(expr, offset, default)` - the value for the row _offset_ rows before the current; _offset_ and _default_ are optional; default values: _offset_ = 1, _default_ = `NULL`
![Example for window functions lead() and lag()](https://learnsql.com/blog/sql-window-functions-cheat-sheet/lead-lag.png)
- [`ntile(n)`](https://learnsql.com/blog/ntile/) - divide rows within a partition as equally as possible into _n_ groups, and assign each row its group number.![Example for window function ntile()](https://learnsql.com/blog/sql-window-functions-cheat-sheet/ntile.png)
- `first_value(expr)` - the value for the first row within the window frame
- `last_value(expr)` - the value for the last row within the window frame![Example for window functions first_value(), last_value()](https://learnsql.com/blog/sql-window-functions-cheat-sheet/first_value-last_value.png)
+ `nth_value(expr, n)` - the value for the _n_-th row within the window frame; _n_ must be an integer![Example for window function nth_value()](https://learnsql.com/blog/sql-window-functions-cheat-sheet/nth_value.png)
+ `first_value()`, `last_value()`, and `nth_value()` do not require an `ORDER BY`. They accept window frame definition (`ROWS`, `RANGE`, `GROUPS`).
## Aggregate Functions

- `avg(expr)` - average value for rows within the window frame
- `count(expr)` - count of values for rows within the window frame
- `max(expr)` - maximum value within the window frame
- `min(expr)` - minimum value within the window frame
- `sum(expr)` - sum of values within the window frame

**ORDER BY and Window Frame:** Aggregate functions do not require an `ORDER BY`. They accept window frame definition (`ROWS`, `RANGE`, `GROUPS`).