#database #rdbms 
Keys establish relationships between different tables which allows for efficient querying and management of data.
# Types of Keys

| ***STUD_NO*** | ***SNAME*** | ***ADDRESS*** | ***PHONE*** |
| ------------- | ----------- | ------------- | ----------- |
| 1             | Shyam       | Delhi         | 123456789   |
| 2             | Rakesh      | Kolkata       | 223365796   |
| 3             | Suraj       | Delhi         | 175468965   |

## Super Key
+ **Group** of single/composite keys that **uniquely identifies the rows** in a column.
+ **Supports NULL** values in rows.
+ **Can contain extra attributes** not necessary for uniqueness. 
+ Ex: {STUD_NO, PHONE}, {STUD_NO, NAME}
## Candidate Key
+ Minimal super key i.e. uniquely identifies, **no extra attributes**.
+ A table can have **multiple** candidate keys (single/composite).
+ Ex: STUD_NO and PHONE are candidate keys.
## Primary Key
+ One of the candidate keys (singe/composite) is selected as primary key.
+ **No NULL** values in rows.
+ Ex: STUD_NO is primary key
## Alternate Key
+ Candidate key **not chosen** as primary key.
+ PHONE is alternate key.
## Foreign Key
+ It is an attribute in a table that references the primary key of another table.
