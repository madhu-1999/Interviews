#sql 
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
- **PostgreSQL** - `SERIAL` implicitly creates a sequence to generate unique values.
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

# Further Reading
1. [[SQL_Basics]]
2. [[Conditional Expressions]]
3. [[Grouping Data]]
4. [[Subqueries and CTEs]]
5. [[Window Functions]] 