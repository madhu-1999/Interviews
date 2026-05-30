#sql #postgres 
# Prerequisite
[[Database/Functions]]
# Definition
A trigger is a function that is invoked automatically whenever an event (`INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`) occurs.
We can specify if the trigger should be invoked `BEFORE` or  `AFTER` the specified event. 
	`TRUNCATE` only supports `BEFORE` triggers.

To create a new trigger, you define a trigger function first, and then bind this trigger function to a table/view.

>[!tip] Triggers on views is a Postgres specific feature!
# Types
## Row level Triggers
They are**triggered once for each row** that is modified, inserted or deleted.
Ex: If you update 100 rows in a table, a row level triggers fires 100 times.
## Statement level Triggers
They are __triggered once per transaction/statement__.
`TRUNCATE` operations only support statement level triggers.
Ex: If you update 100 rows in a table, a statement level triggers fires 1 time.
# Syntax
```sql
create trigger trigger_name
{before | after}{insert | update | delete}
on table_name
-- `for each row` for row level triggers
-- `for each statement` for statement level triggers
[for [each] {row | statement}]
	[when condition]
	execute {procedure|function} trigger_function
	-- execute procedure is older syntax
	-- execute function is newer syntax (Postgres 11+)
```

Ex: Create a trigger to log last name changes to `employee_audits` table whenever last name is updated in `employees` table.
```sql
create or replace function log_last_name_changes()
returns trigger
language plpgsql
$$
begin
	if new.last_name <> old.last_name then
		insert into employee_audits(employee_id, last_name, changed_on) values(old.id, new.last_name, now());
	end if;
	return new;
$$;
```
Here `old` represents the row before the update and `new` represents the new row to be updated.
	Note: `old` and `new`  values can only be accessed in __row-level__ triggers.
```sql
create trigger last_name_changes
before update
on employees
for each row
	execute procedure log_last_name_changes();
```
# Trigger on view
`INSTEAD OF` triggers are a special type of triggers that **intercept** insert, update, and delete operations on views.

It means that when you execute an `INSERT`, `UPDATE`, or `DELETE` statement on a view, PostgreSQL does not directly execute the statement. Instead, it executes the statements defined in the `INSTEAD OF` trigger.

```sql
CREATE TRIGGER trigger_name
INSTEAD OF INSERT OR UPDATE OR DELETE
ON table_name
FOR EACH ROW
	EXECUTE FUNCTION fn_trigger;
```

```sql
CREATE OR REPLACE FUNCTION update_employee_salaries()
RETURNS TRIGGER AS
LANGUAGE PLPGSQL
$$
DECLARE
    p_employee_id INT;
BEGIN
    IF TG_OP = 'INSERT' THEN
	-- insert a new employee
        INSERT INTO employees(name)
        VALUES (NEW.name)
	RETURNING employee_id INTO p_employee_id;

	-- insert salary for the employee
        INSERT INTO salaries(employee_id, effective_date, salary)
	VALUES (p_employee_id, NEW.effective_date, NEW.salary);
    ELSIF TG_OP = 'UPDATE' THEN
        UPDATE salaries
	SET salary = NEW.salary
	WHERE employee_id = NEW.employee_id;

    ELSIF TG_OP = 'DELETE' THEN
        DELETE FROM employees
	WHERE employee_id = OLD.employee_id;
    END IF;
    RETURN NULL;
END;
$$;

CREATE TRIGGER instead_of_employee_salaries
INSTEAD OF INSERT OR UPDATE OR DELETE
ON employee_salaries
FOR EACH ROW
	EXECUTE FUNCTION update_employee_salaries();
```
# `TRUNCATE` Trigger

```sql
CREATE TRIGGER trigger_name
BEFORE TRUNCATE ON table_name
FOR EACH STATEMENT
	EXECUTE FUNCTION trigger_function_name();
```