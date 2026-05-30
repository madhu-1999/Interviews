#sql 
![Recursive Queries in SQL](https://youtu.be/7hZYh9qXxe4)
https://builtin.com/data-science/recursive-sql
# Syntax
```sql
WITH RECURSIVE <CTE_NAME> AS (
	SELECT query -- non recursive or base query
	UNION [ALL]
	SELECT query -- recursive query
	FROM <CTE_NAME>
	[WHERE <termination condition>]
	[JOIN tbl ON condition]
)
```
