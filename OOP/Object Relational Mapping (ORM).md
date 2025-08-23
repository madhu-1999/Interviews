#oop #database #orm 
+ Map objects to database tables i.e map fields in a class to columns in a table
+ Provides a way to interact with database **without having to write any database specific code**
```sql
SELECT id, name, email, country, phone_number FROM users WHERE id = 20;
```
is replaced by
```java
users.getById(20);
```

# Advantages
+ It speeds up development time for teams.
- Decreases the cost of development.
- Abstracts database i.e. database can be changed independently, no need to change code that uses ORM tool.
- Handles the logic required to interact with databases.
- Improves security. ORM tools are built to eliminate the possibility of SQL injection attacks, typos which may be present in raw sql queries.
- You write less code when using ORM tools than with SQL.

# Disadvantages
- Learning how to use ORM tools can be time consuming.
- They are likely not going to perform better when very complex queries are involved.
- ORMs are generally slower than using SQL.

# Further Reading
+ [[Spring Data JPA]]
