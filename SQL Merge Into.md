#sql #de #data-warehouse

`MERGE INTO` handles __inserts, updates and deletes in a single pass__.
This makes it __ideal__ for handling upserts and deletes __for very large datasets__.
	A common use case is while trying to maintain  Slowly changing dimensions (SCD)
	in data warehouse.

# Syntax
```sql
MERGE INTO TargetTable AS Target
USING SourceTable AS Source
ON Target.id = Source.id
WHEN MATCHED THEN
	UPDATE SET Target.column1 = Source.column1 -- Update existing rows
	WHEN NOT MATCHED THEN -- Rows in source but not in target
		INSERT (id, column1) VALUES(Source.id, Source.column1) -- Insert new values
	WHEN NOT MATCHED BY SOURCE THEN
		DELETE; --Remove rows not found in source but in target
```

>[!warning]+
>A `MERGE INTO` must be terminated using a semicolon ( ; ), otherwise a error is raised.

