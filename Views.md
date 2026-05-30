#sql  #postgres 
# View
A **view** is a _virtual table_ that represents the results of a pre-defined SQL query. It does not store data physically. Instead, PostgreSQL executes the underlying query every time you access the view.
>[!warning]
>__SQL Standard__: Views are __read-only__ by default i.e. we cannot insert, update or delete records in a view.
>__Postgres__: Views can be updated only if they meet certain criteria.
# Benefits
- __Query Simplification__: Saves complex SQL queries for repeated use.
- __Security & Data Access Control__: Restricts access to sensitive columns while exposing necessary information.
- __Data Abstraction__: Shields users from changes in underlying database structure.
# Create view
```sql
CREATE OR REPLACE VIEW view_name AS query;
```
# Drop view
```sql
DROP VIEW [IF EXISTS] view_name1, view_name2, ... 
[CASCADE | RESTRICT];
```
# Updatable views
A view can be updated iff:
1. The _query_ defining a view must contain __only one table/view__ in its `FROM` clause.
2. The _query_ defining a view __must not contain__ any of these clauses at the top level:
	1. `GROUP BY`
	2. `HAVING`
	3. `LIMIT`
	4. `OFFSET`
	5. `DISTINCT`
	6. `WITH`
	7. `UNION`
	8. `INTERSECT`
	9. `EXCEPT` 
3. The _selection_ list of the defining _query_ __must not contain__:
	1. [[Window Functions]]
	2. [[Database/Functions#Returning a Table|Set Returning Function]]
	3. Aggregate Functions

==An updatable view may contain both updatable and non-updatable columns==. If you attempt to modify a non-updatable column, PostgreSQL will raise an error.
# Materialized Views
They are views that __store data physically__. 
Materialized views cache the result of an expensive query and its data is refreshed periodically.
They are useful in cases which require __fast data access__ and __underlying data changes infrequently__ such as data warehouses and BI.
```sql
CREATE MATERIALIZED VIEW [IF NOT EXISTS] view_name
AS
query
WITH [NO] DATA;
```

`WITH DATA` - Load data into view at creation time
`WITH NO DATA` - View contains no data at creation time i.e. load manually.
## Loading/ Refreshing data
```sql
-- This locks underlying tables while loading the data
REFRESH MATERIALIZED VIEW view_name;

-- Fetches data while underlying tables are being updated
REFRESH MATERIALIZED VIEW CONCURRENTLY view_name;
```

The `CONCURRENTLY` option requires atleast __one unique index__ on the underlying tables, so that Postgres can uniquely identify which rows to update/delete.
The command creates a temporary table, compares both versions using the unique index performs `INSERT` and `UPDATE` for the differences.
This makes it __slower__ and __requires more storage__ (temp table) than not using the option.

__When to use `CONCURRENTLY`__?
- You cannot afford downtime for `SELECT` queries on the view.
- You are updating a small portion of a large materialized view.
**When NOT to Use `CONCURRENTLY`**?
- You need the fastest possible refresh time (standard is faster).
- You are concerned about storage bloat, as concurrent refreshes can increase it.
>[!warning] Index behavior on refresh
>In a __standard__ refresh, the view is dropped and rebuilt, so indexes are also rebuilt each time. This is an important thing to consider if view holds a large amount of data and needs multiple indexes.
>
>In a __concurrent__ refresh, the view is updated incrementally, so indexes are also updated incrementally.
# Recursive View
A recursive view is a view whose defining query is a [[Recursive Queries|recursive query]]. It does not store data physically.
```sql
CREATE RECURSIVE VIEW view_name(columns)
AS
query;
```