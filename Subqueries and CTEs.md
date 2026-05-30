#sql #postgres 
# Subquery
+ Query nested in another query
## In WHERE clause

**Scenario:** Find all employees who earn more than the company average.

Table:  `Employees` 

| **id** | **name** | **salary** |
| ------ | -------- | ---------- |
| 1      | Alice    | 90000      |
| 2      | Bob      | 60000      |
| 3      | Charlie  | 45000      |
```sql
SELECT 
	name, 
	salary
FROM employees
WHERE 
	salary > (SELECT AVG(salary) FROM employees);
```

Output:

| **name** | **salary** |
| -------- | ---------- |
| Alice    | 90000      |
## In FROM clause
When you need to perform an aggregation (like a sum or count) and then perform another operation on that result, you can treat the subquery as a temporary table.

**Scenario:** Calculate the average total sales per department.

Table:  `Sales`

| **dept** | **sale_amount** |
| -------- | --------------- |
| Tech     | 1000            |
| Tech     | 2000            |
| HR       | 500             |
```sql
SELECT AVG(dept_total) as avg_department_spend
FROM (
    SELECT dept, SUM(sale_amount) as dept_total
    FROM sales
    GROUP BY dept
) AS sales_summary;
```

Output:

| **avg_department_spend** |
| ------------------------ |
| 1750.00                  |
## Existence Check (EXISTS/ IN)
+ Check if a related record exists in another table without performing a full `JOIN`. 
+ This is often more performant when you only care if a match exists, not the data within it.

**Scenario:** List customers who have ordered a laptop.
Table: `Customers`

| name | phone        | id  |
| ---- | ------------ | --- |
| Joe  | 555-678-8372 | 1   |
| Ann  | 323-843-9018 | 2   |
Table: `Orders`

| id  | customer_id | product |
| --- | ----------- | ------- |
| 1   | 2           | Laptop  |
```sql
SELECT name
FROM customers c
WHERE EXISTS (
    SELECT 1 
    FROM orders o 
    WHERE product = 'Laptop'
);

-- OR
SELECT name
FROM customers c
WHERE id IN (
    SELECT customer_id
    FROM orders o 
    WHERE product = 'Laptop'
);
```

| **name** |
| -------- |
| Ann      |
## Scalar Subqueries in SELECT
Use a subquery to bring in a single value from another table for every row in your result set.

**Scenario:** Show each product alongside the total number of products in its category.
Table `products`

| **product** | **category** |
| ----------- | ------------ |
| iPhone      | Tech         |
| Galaxy      | Tech         |
| Desk        | Office       |
```sql
SELECT 
    product, 
    category,
    (SELECT COUNT(*) FROM products p2 WHERE p2.category = p1.category) as cat_count
FROM products p1;
```

Output:

| **product** | **category** | **cat_count** |
| ----------- | ------------ | ------------- |
| iPhone      | Tech         | 2             |
| Galaxy      | Tech         | 2             |
| Desk        | Office       | 1             |
## Subquery with ANY/ALL operators
ANY -> multiple OR conditions
ALL -> multiple AND conditions

**Scenario**: Find employees who have salaries greater than the manager’s salaries
Table : `Employees`

| **id** | **first_name** | **last_name** | **salary** |
| ------ | -------------- | ------------- | ---------- |
| 1      | Bob            | Williams      | 45000.00   |
| 2      | Charlie        | Davis         | 55000.00   |
| 3      | David          | Jones         | 50000.00   |
| 4      | Emma           | Brown         | 48000.00   |
| 5      | Frank          | Miller        | 52000.00   |
| 6      | Grace          | Wilson        | 49000.00   |
| 7      | Harry          | Taylor        | 53000.00   |
| 8      | Ivy            | Moore         | 47000.00   |
| 9      | Jack           | Anderson      | 56000.00   |
| 10     | Kate           | Hill          | 44000.00   |
| 11     | Liam           | Clark         | 59000.00   |
| 12     | Mia            | Parker        | 42000.00   |
Table: `Managers`

| **id** | **first_name** | **last_name** | **type** | **salary** |
| ------ | -------------- | ------------- | -------- | ---------- |
| 1      | John           | Doe           | manager  | 60000.00   |
| 2      | Jane           | Smith         | manager  | 55000.00   |
| 3      | Alice          | Johnson       | manager  | 58000.00   |

```sql
SELECT * 
FROM employees 
WHERE salary > ANY ( SELECT salary FROM managers );
```

**Scenario**: Find all employees who have salaries greater than all managers.
```sql
SELECT * 
FROM employees 
WHERE salary > ALL( select salary from managers );
```

## Correlated Subquery
+ A subquery that references columns in the outer query.
+ The subquery is evaluated **once per row in the outer query's result set**.
	+ Significant performance degradation with large datasets
## Compare rows to an aggregate within a group
**Scenario**:  Find films that are longer than the average length of all films with the **same rating**.

| film_id | title            | length | rating |     |
| :------ | :--------------- | :----- | :----- | --- |
| 1       | Academy Dinosaur | 86     | PG     |     |
| 2       | Ace Goldfinger   | 48     | G      |     |
| 3       | Adaptation Holes | 50     | G      |     |
| 4       | Affair Prejudice | 117    | G      |     |
```sql
SELECT film_id, title, length, rating
FROM film f
WHERE length > (
    SELECT AVG(length)
    FROM film
    WHERE rating = f.rating
);
```

| film_id | title            | length | rating |     |
| :------ | :--------------- | :----- | :----- | --- |
| 4       | Affair Prejudice | 117    | G      |     |
## EXISTS check
**Scenario**: Find customers who have made at least one payment of more than $10.
Table: `customer`

| id  | name     |
| --- | -------- |
| 10  | John Doe |
Table:`payment`

| id  | amount |
| --- | ------ |
| 10  | 2.99   |
| 10  | 11.99  |
| 10  | 5.99   |
```sql
SELECT name
FROM customer c
WHERE EXISTS (
    SELECT 1
    FROM payment p
    WHERE p.customer_id = c.customer_id
    AND p.amount > 10
);
```

# CTE
+ A temporary, named result set in SQL that allows you to simplify complex queries, making them easier to read and maintain.
+ Commonly used when working with multiple subqueries.
+ Helps avoid duplication by allowing the same result set to be reused.(if `materialized`)
```sql
WITH cte_name (column1, column2, ...) AS (
    -- Query that defines the CTE
    SELECT ...
    FROM ...
    WHERE ...
)
-- Main query
SELECT ...
FROM cte_name;
```

**Scenario**: Suppose we have three tables: `Orders`, `Customers`, and `Products`. We want to find the total revenue generated by each customer who purchased in 2024.

Without CTE:
```sql
-- Standard SQL: Hard to read nested logic
SELECT 
    c.CustomerName, 
    SUM(p.Price * o.Quantity) AS TotalRevenue
FROM Orders o
JOIN Customers c 
    ON o.CustomerID = c.CustomerID
JOIN Products p 
    ON o.ProductID = p.ProductID
WHERE EXTRACT(YEAR FROM o.OrderDate) = 2024
GROUP BY c.CustomerName
HAVING SUM(p.Price * o.Quantity) > 1000;
```

With CTE:
```sql
-- Standard SQL: Cleaner with CTE
WITH OrderDetails AS (
    SELECT 
        o.OrderID, 
        c.CustomerName, 
        p.Price, 
        o.Quantity, 
        o.OrderDate
    FROM Orders o
    JOIN Customers c 
        ON o.CustomerID = c.CustomerID
    JOIN Products p 
        ON o.ProductID = p.ProductID
    WHERE EXTRACT(YEAR FROM o.OrderDate) = 2024
)
-- Main query
SELECT 
    CustomerName, 
    SUM(Price * Quantity) AS TotalRevenue
FROM OrderDetails
GROUP BY CustomerName
HAVING SUM(Price * Quantity) > 1000;
```
## Recursive CTE