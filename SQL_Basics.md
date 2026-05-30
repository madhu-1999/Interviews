---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.19.1
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---
#sql
```python
!pip install ipython-sql sqlalchemy psycopg2
```

```python
# Setup
%load_ext sql
%config SqlMagic.style = '_DEPRECATED_DEFAULT'
%sql postgresql://postgres:asap0123@localhost/dvdrental
```

# Dvdrental ERD
![ERD](../Assets/dvd-rental.avif)


# Table of Contents
1. [SELECT](#1)     
    1. [Basic Syntax](#2) 
    2. [Select All Columns](#3)
    3. [Select With Expression](#4)
    4. [Select From multiple tables](#5)
    5. [Select Distinct](#6)
    6. [Select Distinct On](#7)
2. [ORDER BY](#8) 
    1. [Basic Syntax](#9)
    2. [With Nulls](#10)
3. [WHERE](#11) 
    1. [Basic Syntax](#12)
    2. [AND/OR Operator](#13)
    3. [IN Operator](#14)
    4. [LIKE/ILIKE Operator](#15)
    5. [BETWEEN Operator](#35)
    6. [IS NULL Operator](#16)
4. [LIMIT/OFFSET](#17)     
5. [Joins](#18)
    1. [INNER JOIN](#19)
    2. [LEFT JOIN](#20)
    3. [LEFT ANTI JOIN](#21)
    4. [RIGHT JOIN](#22)
    5. [RIGHT ANTI JOIN](#23)
    6. [FULL OUTER JOIN](#24)
    7. [FULL ANTI JOIN](#25)
    8. [Self Join](#26)
    9. [CROSS JOIN](#27)
    10.[NATURAL JOIN](#28)
6. [GROUP BY](#29)     
    1. [HAVING Clause](#30)      
7. [Set Operators](#31)       
    1. [UNION/UNION ALL](#32)      
    2. [INTERSECT](#33)     
    3. [EXCEPT](#34)         
4. [LIMIT/OFFSET](#17)     
5. [Joins](#18)
    1. [INNER JOIN](#19)
    2. [LEFT JOIN](#20)
    3. [LEFT ANTI JOIN](#21)
    4. [RIGHT JOIN](#22)
    5. [RIGHT ANTI JOIN](#23)
    6. [FULL OUTER JOIN](#24)
    7. [FULL ANTI JOIN](#25)
    8. [Self Join](#26)
    9. [CROSS JOIN](#27)
    10.[NATURAL JOIN](#28)
6. [GROUP BY](#29)     
    1. [HAVING Clause](#30)      
7. [Set Operators](#31)       
    1. [UNION/UNION ALL](#32)      
    2. [INTERSECT](#33)     
    3. [EXCEPT](#34)         


 # SELECT <a id="1"></a>
 
 ## Basic Syntax <a id="2"></a>
 
 ```sql
 SELECT column1, column2, ...
FROM table_name;
 ```

```sql
SELECT actor_id 
FROM actor
LIMIT 5;
```

<!-- #region -->
## Select All Columns <a id="3"></a>
```sql
SELECT *
 FROM table_name;
```
<!-- #endregion -->

```sql
SELECT * 
FROM actor
LIMIT 5;
```

<!-- #region -->
## Select With Expression <a id="4"></a>
__AS__ keyword is used to provide column alias

```sql
SELECT expr AS column_alias
FROM table_name
```
<!-- #endregion -->

```sql
SELECT
    actor_id,
    -- || ' ' || is concat operation
    first_name || ' ' || last_name AS name 
FROM
   actor
LIMIT 5;
```

<!-- #region -->
If column alias contains __spaces__ then surround it with " ".
```sql
column_name AS "column alias"
```
<!-- #endregion -->

```sql
SELECT
    first_name || ' ' || last_name AS "full name"
FROM
    actor
LIMIT 5;
```

<!-- #region -->
## Select from multiple tables <a id="5"></a>
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1, table2 t2
```
This performs a cross join


**Ex: For each department and manager in Company X, find all (department, manager) pairings such that the _manager_ has never been assigned to manage the _department_**

**Department Table**

| dept_no | dept_name   |
| :------ | :---------- |
| d01     | Sales       |
| d02     | Engineering |

**Employee Table**

| emp_no | birth_date | first_name | last_name | gender | hire_date  |
| :----- | :--------- | :--------- | :-------- | :----- | :--------- |
| 1001   | 1985-04-12 | James      | Holden    | M      | 2015-02-10 |
| 1002   | 1988-11-20 | Naomi      | Nagata    | F      | 2016-08-01 |

**Manager Table**

| emp_no | dept_no | from_date  | to_date    |
| :----- | :------ | :--------- | :--------- |
| 1001   | d01     | 2020-01-01 | 9999-01-01 |
| 1002   | d02     | 2021-03-15 | 9999-01-01 |

**Output**

| dept_name   | first_name | last_name |     |
| :---------- | :--------- | :-------- | --- |
| Sales       | Naomi      | Nagata    |     |
| Engineering | James      | Holden    |     |
<!-- #endregion -->

```sql
SELECT 
    d.dept_name,
    e.first_name,
    e.last_name
FROM 
    public.department d
CROSS JOIN 
    public.employee e
-- We filter employees to only those who have BEEN managers at some point
INNER JOIN 
    public.dept_manager dm_filter ON e.emp_no = dm_filter.emp_no
-- We Left Join back to the actual assignments to see where matches DO NOT exist
LEFT JOIN 
    public.dept_manager dm ON d.dept_no = dm.dept_no AND e.emp_no = dm.emp_no
WHERE 
    dm.from_date IS NULL
ORDER BY 
    d.dept_name;
```

<!-- #region -->
## Select Distinct <a id="6"></a>

This clause removes duplicate rows from result set.
```sql
SELECT
  DISTINCT column1
FROM
  table_name;
```
<!-- #endregion -->

```sql
SELECT DISTINCT
  rental_rate
FROM
  film
ORDER BY
  rental_rate;
```

<!-- #region -->
If multiple columns are specified, all column values are considered when deciding duplicates.
```sql
SELECT
   DISTINCT column1, column2
FROM
   table_name;
```
We __cannot__ specify that `DISTINCT` clause should only be applied on a subset of columns in the `SELECT`clause.
```sql
SELECT
-- Distinct on column1 & column2 but not on column3. This is NOT valid.
   DISTINCT (column1, column2), column3
FROM
   table_name;
```
<!-- #endregion -->

```sql
SELECT 
    DISTINCT film_id, store_id
FROM
    inventory
ORDER BY film_id
LIMIT 5;
```

<!-- #region -->
## Select Distinct on <a id="7"></a>
This clause removes duplicate rows from result set, based on subset of columns in `SELECT` clause
```sql
SELECT
  DISTINCT ON (column1, column2,...) column1,
  column2,
  ...
FROM
  table_name
ORDER BY
  column1,
  column2,
  ...;
```
The _first_ unique entry of specified columns in `DISTINCT ON` clause is picked as _representative_.
\nWithout `ORDER BY` the _first_ unique entry is picked randomly.

Lets understand difference between `DISTINCT ON` and `DISTINCT` clause.
Below query returns every unique combination of (_customer_id_, _rental_date_) encountered, since `DISTINCT` is applied to both columns. 
<!-- #endregion -->

```sql
SELECT DISTINCT customer_id, rental_date 
FROM rental 
ORDER BY customer_id
LIMIT 5;
```

But what if I want to find the first rental date for each customerId?
Here we use `DISTINCT ON` clause as follows:

```sql
SELECT DISTINCT ON (customer_id) 
    customer_id, 
    rental_date
FROM rental
ORDER BY customer_id, rental_date ASC
LIMIT 5;
```

<!-- #region -->
# ORDER BY <a id="8"></a>
## Basic Syntax <a id="9"></a>
```sql
SELECT
  select_list
FROM
  table_name
ORDER BY
  sort_expression1 [ASC | DESC][NULLS FIRST | NULLS LAST],
  sort_expression2 [ASC | DESC][NULLS FIRST | NULLS LAST],
  ...;
```
Note: ASC is default, so it can be omitted.
<!-- #endregion -->

```sql
-- Fetch all customer names in alphabetical order of first_name and descending order of last_name.
SELECT
  first_name,
  last_name
FROM
  customer
ORDER BY
  first_name,
  last_name DESC
LIMIT 5;
```

<!-- #region -->
## With NULLs <a id="10"></a>
When you sort rows that contain NULL, you can specify the order of NULL by using the `NULLS FIRST` or `NULLS LAST` option of the ORDER BY clause:
```sql
ORDER BY sort_expresssion [ASC | DESC] [NULLS FIRST | NULLS LAST]
```

The `NULLS FIRST` option places NULL before other non-null values
<!-- #endregion -->

```sql
--List rentals and prioritize those that have not been returned
SELECT rental_id, return_date
FROM rental
ORDER BY return_date ASC NULLS FIRST
LIMIT 5;
```

The `NULLS LAST` option places NULL after other non-null values.

```sql
--List all films and their return dates, showing those already returned first.
SELECT rental_id, return_date
FROM rental
ORDER BY return_date ASC NULLS LAST
LIMIT 5;
```

<!-- #region -->
# WHERE Clause <a id="11"></a>
## Basic Syntax <a id="12"></a>
```sql
SELECT
  select_list
FROM
  table_name
WHERE
  condition
ORDER BY
  sort_expression;
```
Following is a list of all operators that can be used in the `WHERE` clause.
| Operator | Description |
| :--- | :--- |
| `=` | Equal |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |
| `<>` or `!=` | Not equal |
| `AND` | Logical operator AND |
| `OR` | Logical operator OR |
| `IN` | Return true if a value matches any value in a list |
| `BETWEEN` | Return true if a value is between a range of values |
| `LIKE` | Return true if a value matches a pattern |
| `IS NULL` | Return true if a value is NULL |
| `NOT` | Negate the result of other operators |
<!-- #endregion -->

## AND/OR operators <a id="13"></a>
A __boolean__ has 3 values: _true_ (t), _false_(f) or _null_.

true AND null -> null
null AND null -> null
false AND null -> false

true OR null -> true
null OR null -> null
false OR null -> null

<!-- #region -->
## IN operator <a id="14"></a>
`IN` operator is used to find value in a list
```sql
value IN (value1,value2,...)
```
Functionally, the `IN` operator is equivalent to combining multiple boolean expressions with the `OR` operators:
```sql
value = value1 OR value = value2 OR ...
```
<!-- #endregion -->

```sql
--With list of strings
SELECT
  first_name,
  last_name
FROM
  customer
WHERE
  first_name IN ('Ann', 'Anne', 'Annie');
```

```sql
--With list of numbers
SELECT
  film_id,
  title
FROM
  film
WHERE
  film_id in (1, 2, 3);
```

```sql
--With list of dates
SELECT
  payment_id,
  amount,
  payment_date
FROM
  payment
WHERE
--payment is of type timestamp. It is cast to type date for the check.
  payment_date::date IN ('2007-02-15', '2007-02-16')
LIMIT 5;
```

<!-- #region -->
## Negating with NOT IN operator
To negate the `IN` operator, you use the `NOT IN` operator
```sql
value NOT IN (value1, value2, ...)
```

The `NOT IN` operator is equivalent to a combination of multiple boolean expressions with the `AND` operators:
```sql
value <> value1 AND value <> value2 AND ...
```
<!-- #endregion -->

```sql
SELECT
  film_id,
  title
FROM
  film
WHERE
  film_id NOT IN (1, 2, 3)
ORDER BY
  film_id
LIMIT 5;
```

<!-- #region -->
## LIKE Operator <a id="15"></a>
`LIKE` operator is used to match a pattern. (__case-sensitive__)

PostgreSQL offers two wildcards:
+ Percent sign (%) matches any sequence of zero or more characters.
+ Underscore sign (_)  matches any single 

```sql
value LIKE pattern

-- To negate
value NOT LIKE pattern
```

Note: For __case-insensitive__ matching use `ILIKE` and `NOT ILIKE` operators
<!-- #endregion -->

```sql
--LIKE operator is used to match a pattern
SELECT
  first_name,
  last_name
FROM
  customer
WHERE
  first_name LIKE 'Ann%';
```

<!-- #region -->
## BETWEEN operator <a id="35"></a>
The `BETWEEN` operator allows you to check if a value falls within a range of values.
```sql
value BETWEEN low AND high;
--which is the same as 
value >= low AND value <= high
```
<!-- #endregion -->

```sql
SELECT
  payment_id,
  amount
FROM
  payment
WHERE
  payment_id BETWEEN 17503 AND 17505
ORDER BY
  payment_id;
```

<!-- #region -->
### Negating with NOT BETWEEN
If you want to check if a value is outside a specific range, you can use the `NOT BETWEEN` operator
```sql
value NOT BETWEEN low AND high
-- which is the same is
value < low OR value > high
```
<!-- #endregion -->

<!-- #region -->
## IS NULL Operator <a id="16"></a>
The `IS NULL` operator returns true if the value is NULL or false otherwise
```sql
value IS NULL

-- To negate
value IS NOT NULL
```
<!-- #endregion -->

```sql
SELECT
  address,
  address2
FROM
  address
WHERE
  address2 IS NULL;
```

<!-- #region -->
# LIMIT/OFFSET Clause <a id="17"></a>
```sql
SELECT
  select_list
FROM
  table_name
ORDER BY
  sort_expression
LIMIT
  row_count
OFFSET
  row_to_skip;
```
<!-- #endregion -->

```sql
-- retrieve 4 films starting from the fourth one
SELECT
  film_id,
  title,
  release_year
FROM
  film
ORDER BY
  film_id
LIMIT 4 OFFSET 3;
```

<!-- #region -->
## Alternative to LIMIT -> FETCH clause
Functionally, they are the same.
\n If you plan to make your application compatible with other database systems, you should use the FETCH clause.

```sql
OFFSET row_to_skip { ROW | ROWS }
FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY
```

Note: there is **NO** functional **difference** between `FIRST` and `NEXT`.

FIRST: Often used when you want the very beginning of a result set (e.g., FETCH FIRST 10 ROWS ONLY).

NEXT: Frequently used when implementing pagination alongside an OFFSET to indicate you are grabbing the "next" batch of records.
<!-- #endregion -->

```sql
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title
OFFSET 5 ROWS
FETCH FIRST 5 ROW ONLY;
```

# JOINS <a id="18"></a>
## Inner Join <a id="19"></a>

The inner join examines each row in the first table. It compares the value in the t1_id column with the value in the id column of each row in the second table. If these values are equal, the inner join creates a new row that contains columns from both tables and adds this new row to the result set.
![Inner Join](attachment:image.png)

```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
JOIN inventory i 
    ON i.film_id = f.film_id
ORDER BY 
    i.inventory_id
LIMIT 5;
```

## Left Join <a id="20"></a>
![image.png](attachment:image.png)

```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
LEFT JOIN inventory i 
    ON i.film_id = f.film_id
ORDER BY 
    i.inventory_id NULLS FIRST,
    f.film_id
LIMIT 5;
```

## Left Anti-Join <a id="21"></a>
A left join that **only** returns rows from the left table that do not have matching rows from the right table:
![image.png](attachment:image.png)

```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
LEFT JOIN inventory i 
    ON i.film_id = f.film_id
WHERE 
    i.film_id IS NULL
ORDER BY 
    f.film_id
LIMIT 5;
```

## Right Join <a id="22"></a>
![image.png](attachment:image.png)

```sql
SELECT
    c.customer_id,
    c.first_name,
    c.last_name,
    r.rental_id
FROM
    rental r
RIGHT JOIN customer c 
    ON r.customer_id = c.customer_id
ORDER BY 
    c.last_name
LIMIT 5;
```

## Right Anti Join <a id="23"></a>
![image.png](attachment:image.png)

```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
FULL OUTER JOIN inventory i 
    ON i.film_id = f.film_id
WHERE 
    i.film_id IS NULL
LIMIT 5;
```

## Full Join <a id="24"></a>
![image.png](attachment:image.png)

```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
FULL OUTER JOIN inventory i 
    ON i.film_id = f.film_id
LIMIT 5;
```

## Full Anti-Join <a id="25"></a>
![image.png](attachment:image.png)
```sql
SELECT
    f.film_id,
    f.title,
    i.inventory_id
FROM
    film f
FULL OUTER JOIN inventory i 
    ON i.film_id = f.film_id
WHERE 
    f.film_id IS NULL OR i.film_id IS NULL
LIMIT 5;
```

## Self join <a id="26"></a>
![image.png](attachment:image.png)

```sql
--Find customers with same last name no duplicates
SELECT
    c1.first_name || ' ' || c1.last_name AS customer_1,
    c2.first_name || ' ' || c2.last_name AS customer_2,
    c1.last_name
FROM
    customer c1
INNER JOIN customer c2 
    ON c1.last_name = c2.last_name
    AND c1.customer_id > c2.customer_id
ORDER BY 
    c1.last_name;
```

## Cross Join <a id="27"></a>
 It returns all possible combinations. For example, if you have 200 actors and 16 categories, the result will contain exactly $200 \times 16 = 3,200$ rows.
![image.png](attachment:image.png)

```sql
SELECT 
    a.first_name, 
    a.last_name, 
    c.name AS category_name
FROM 
    actor a
CROSS JOIN category c
ORDER BY 
    a.last_name, 
    c.name
LIMIT 5;
```

<!-- #region -->
## NATURAL JOIN <a id="28"></a>
```sql
SELECT select_list
FROM table1
NATURAL [INNER, LEFT, RIGHT] JOIN table2;
```
A natural join can be an _inner join, left join, or right join_. 
If you do not specify an explicit join, PostgreSQL will use the `INNER JOIN` by default.

It does not require you to specify the condition in the join clause because it uses an implicit condition based on the equality of __ALL__ the common columns.

Consider the city and country tables. Both tables have the same `country_id` column 
<!-- #endregion -->

```sql
SELECT *
FROM city
NATURAL JOIN country;
```

The query returns an empty result set.

The reason is that both tables have another common column called `last_update`. When the `NATURAL JOIN` clause uses the `last_update` column, it does not find any matches.

<!-- #region -->
# GROUP BY Clause <a id="29"></a>
```sql
SELECT
   column_1,
   column_2,
   ...,
   aggregate_function(column_3)
FROM
   table_name
GROUP BY
   column_1,
   column_2,
   ...;
```

<div class="alert alert-block alert-danger">
    <b>Warning:</b> <p>All <b>unaggregated</b> columns <b>MUST</b> be included in the `GROUP BY` clause</p>.
<p>This query does not work since `city_of_delivery` is not included in the `GROUP BY` clause.</p>
</div>
    
```sql
SELECT
  country_of_delivery,
  city_of_delivery,
  SUM(number_of_packages) total_number_of_packages
FROM 
    package_deliveries
GROUP BY 
    country_of_delivery;
```

<!-- #endregion -->

## Count()
1. __COUNT(*)__ to count the number of rows in the table.

```sql
SELECT COUNT(*)
FROM rental;
```

2. **COUNT(expr)** to count the number of **non-null** values in a given column.

```sql
SELECT COUNT(return_date)
FROM rental;
```

3. **COUNT(DISTINCT column)**  to count the number of distinct values in a column.

```sql
SELECT  COUNT(district) - COUNT(DISTINCT district) AS Null_district_count
FROM address
```

## SUM()
1. __SUM()__ with a column or expression

```sql
--Calculate total revenue from rentals
SELECT
    SUM(amount) as total_revenue
FROM payment;
```

```sql
--Find total replacement cost for films longer than 2 hours in the inventory.
SELECT 
    SUM(replacement_cost * 1.1) AS inflated_replacement_cost
FROM 
    film
WHERE 
    length > 120;
```

2. __SUM(DISTINCT col)__ to sum up unique values

```sql
-- Find the sum of unique payment amounts processed by each staff member
SELECT 
    staff_id, 
    SUM(DISTINCT amount) AS sum_unique_payments
FROM 
    payment
GROUP BY 
    staff_id;
```

Other aggregate functions are
+ AVG()
+ MIN()
+ MAX()

<div class="alert alert-block alert-danger">
    AVG() <b>ignores</b> NULL values, which can cause a <i>missing value</i> error
</div>

```sql
SELECT 
    staff_id,
    -- Standard AVG: Only looks at rows where return_date is NOT NULL
    AVG(return_date - rental_date) AS avg_returned_duration,
    
    -- Adjusted AVG: Treats NULLs as 0 to see how active rentals lower the average
    AVG(COALESCE(return_date - rental_date, '0 days')) AS avg_including_active
FROM 
    rental
GROUP BY 
    staff_id;
```

<!-- #region -->
## HAVING Clause <a id="30"></a>
Filter aggregated results from `GROUP BY`
```sql
SELECT
  column1,
  column2,
  ...,
  aggregate_function(column_name)
FROM table_name
GROUP BY column1, column2, …
HAVING filtering_condition;
```
<!-- #endregion -->

```sql
SELECT 
    customer_id, 
    SUM(amount) AS total_spent
FROM 
    payment
GROUP BY 
    customer_id
HAVING 
    SUM(amount) > 200
ORDER BY 
    total_spent DESC;
```

<!-- #region -->
# Set Operators <a id="31"></a>
The `EXCEPT`, `UNION` and `INTERSECT` operators will always sort the returned results.<br>
`UNION ALL` will not sort the returned results. (Can sort by using an `ORDER BY` at the end of the query.)
<div class="alert alert-box alert-warning">
    We can't use <code>ORDER</code> BY before a SQL set operator or try to sort every SELECT separately.
</div>

## Union / Union ALL <a id="32"></a>
`UNION/UNION ALL` **vertically** combines the result set of two or more SELECT statements.
Note: Use Cross Join / Select from multiple tables to combine horizontally.
```sql
SELECT column_1, column_2
FROM table_1
 
UNION [ALL]
 
SELECT column_1, column_2
FROM table_2
[ORDER BY column1, column2, ...]
```
<div class="alert alert-box alert-warning">
    Rules for <code>UNION/UNION ALL</code>:
    <ul>
        <li>Both `SELECT` statements should list the same number of columns, with the same type.</li>
        <ul>
            <li>Note that the columns are merged in order i.e. col1 in first query is merged with col1 in second query, etc...</li>
        </ul>
        <li><code>UNION</code> keeps only unique values in the result set, while <code>UNION ALL</code> retains duplicates.</li>
    </ul>
</div>

<div class="alert alert-box alert-info">
    <code>UNION</code> performs a <code>SELECT DISTINCT</code> under the hood. If the values in a column are guaranteed to be unique, use <code>UNION ALL</code> instead.
</div>

1. `UNION`
If you want to know which City IDs are actually being used by addresses in our system without seeing the same ID multiple times, you use `UNION`
<!-- #endregion -->

```sql
-- This finds every City ID that exists in the city list 
-- AND every City ID currently assigned to an address.
-- It removes duplicates where they overlap.
SELECT city_id FROM city
UNION
SELECT city_id FROM address
ORDER BY city_id;
```

We get a clean list from 1 to 600. Even though city_id 300 exists in the city table and is used by 5 different customers in the address table, it appears only once.

If you wanted to see the raw count of entries, you would use `UNION ALL`

```sql
-- This dumps both lists into one giant bucket.
SELECT city_id FROM city
UNION ALL
SELECT city_id FROM address
ORDER BY city_id;
```

Below is an example of combining columns with different column names but same data type

```sql
-- Combine staff_id and customer_id into one list
-- Both are INTEGER types, though the column names differ.
SELECT staff_id AS user_id, 'Staff' AS user_type
FROM staff
UNION
SELECT customer_id, 'Customer' AS user_type
FROM customer
ORDER BY user_id
LIMIT 5;
```

<!-- #region -->
## Intersect Operator <a id="33"></a>
`INTERSECT` **vertically** combines the result set of two or more SELECT statements and returns **distinct** rows available in both result sets.
```sql
SELECT select_list
FROM A
INTERSECT
SELECT select_list
FROM B
[ORDER BY order_list];
```
<div class="alert alert-box alert-warning">
    Rules for <code>INTERSECT</code>:
    <ul>
        <li>Both `SELECT` statements should list the same number of columns, with the same type.</li>
        <li>Note that the columns are merged in order i.e. col1 in first query is merged with col1 in second query, etc...</li>
    </ul>
</div>

<!-- #endregion -->

```sql
-- This creates a list of every UNIQUE first name in the system.
-- If 'PENELOPE' is both an actor and a customer, she appears once.
SELECT first_name FROM actor
UNION
SELECT first_name FROM customer
ORDER BY first_name
LIMIT 5;
```

<!-- #region -->
## Except Operator <a id="34"></a>
`EXCEPT` **vertically** combines the result set of two or more SELECT statements and returns **distinct** rows available only in the first result set.
```sql
SELECT select_list
FROM A
EXCEPT
SELECT select_list
FROM B
[ORDER BY order_list];
```
<div class="alert alert-box alert-warning">
    Rules for <code>EXCEPT</code>:
    <ul>
        <li>Both `SELECT` statements should list the same number of columns, with the same type.</li>
        <li>Note that the columns are merged in order i.e. col1 in first query is merged with col1 in second query, etc...</li>
    </ul>
</div>

<!-- #endregion -->

```sql
-- Returns first and last names of customers who are NOT staff members
SELECT first_name, last_name FROM customer
EXCEPT
SELECT first_name, last_name FROM staff
ORDER BY last_name
LIMIT 5;
```
