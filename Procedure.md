#sql #postgres 
# Prerequisite
[Block Structure](Block%20Structure.md)
# Syntax
```sql
create [or replace] procedure procedure_name(parameter_list)
language plpgsql
as $$
declare
-- variable declaration
begin
-- stored procedure body
end; 
$$;

-- calling a procedure
CALL procedure_name(args_list);
```

>[!important] Procedures do not return a value
# Parameter modes
| IN                                 | INOUT                                                   |
| ---------------------------------- | ------------------------------------------------------- |
| The default                        | Explicitly specified                                    |
| Pass a value to function           | Pass a value to a function and return an updated value. |
| `in` parameters act like constants | `inout` parameters act like initialized variables       |
| Cannot be assigned a value         | Should be assigned a value                              |
```sql
CREATE PROCEDURE proc1(INOUT p1 TEXT)
AS $$
BEGIN
	p1 = concat('!!', p1, '!!')
	RAISE NOTICE 'Procedure parameter: %', p1;
END;
$$;

-- calling the procedure
mydb=# CALL proc1 ('Stored Procedure supported in PostgreSQL');  
NOTICE:  Procedure Parameter: !! Stored Procedure supported in PostgreSQL !!  
                       p1  
--------------------------------------------------------  
!! Stored Procedure supported in PostgreSQL !!  
(1 row)
```
# Transaction control
```sql
-- Check if idx is multiple of 10
-- idx is multiple of 10 - commit the insert
-- idx is not multiple of 10 - roll back
CREATE PROCEDURE proc2()
LANGUAGE plpgsql
AS $$
BEGIN
   FOR idx IN 1..100 LOOP
       INSERT INTO my_tbl(col_num) VALUES(idx);
       IF idx % 10 = 0 THEN 
            COMMIT; 
        ELSE
            ROLLBACK; 
        END IF;
    END LOOP;
END
$$;
```
## Calling a procedure in another procedure.
```sql
CREATE PROCEDURE proc3() LANGUAGE plpgsql AS $$  
BEGIN  
INSERT INTO my_tbl VALUES(1); -- A  
 CALL proc4();  
INSERT INTO my_tbl VALUES(4); -- D  
 COMMIT;  
END;  
$$;

CREATE PROCEDURE proc4() LANGUAGE plpgsql AS $$  
BEGIN  
INSERT INTO my_tbl VALUES(2); -- B  
 ROLLBACK;  
INSERT INTO my_tbl VALUES(3); -- C  
END;  
$$;
```

![Pasted image 20260502112545](Assets/Pasted%20image%2020260502112545.png)
# When to use
1. **Large-Scale Data Processing:** Use procedures when performing heavy operations on millions of rows. Running logic on the database server prevents "shuttling" massive amounts of data over the network to the application server, which reduces latency and saves bandwidth.
2. **Complex Multi-Step Transactions:** They are ideal for operations that span multiple tables and require atomicity (all-or-nothing execution), such as order processing systems that must simultaneously insert an order, update stock, and generate an invoice.
3. **Centralized Business Logic:** If multiple different applications (e.g., a web app, a mobile app, and a reporting tool) access the same database, using stored procedures ensures they all apply the same business rules and data validation logic consistently.
4. **Enhanced Security Layers:** They allow you to follow the "principle of least privilege" by granting users permission to execute a specific procedure without giving them direct access to the underlying tables. This also acts as a robust defense against **SQL injection**.
# When to avoid
1. **Database Portability:** Because stored procedures use database-specific languages (e.g., [T-SQL for SQL Server](https://learn.microsoft.com/en-us/sql/t-sql/language-reference), [PL/SQL for Oracle](https://www.oracle.com/database/technologies/appdev/plsql.html)), they create "vendor lock-in," making it difficult to migrate to a different database provider.
2. **Simple CRUD Operations:** For basic Create, Read, Update, and Delete tasks, modern ORMs are typically more productive and easier to maintain than writing individual procedures for every table.
3. **Scaling Concerns:** Database CPU is often more expensive and harder to scale horizontally than application server CPU. Moving too much logic to the database can create a performance bottleneck. i.e pick and choose what should be a procedure and what can be done on application side.