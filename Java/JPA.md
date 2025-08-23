#java #orm #jpa
+ Stands for Java Persistence API / Jakarta Persistence
+ It is a **specification** for [[Object Relational Mapping (ORM)|ORM]] implementation i.e. it defines the components( interfaces and classes blueprint) and how they should interact but does not provide implementation.
+ ORM tools like Hibernate, OpenJPA provide implementation

# Key Terminologies
## Entity
+ This is the object to table mapping i.e. mapping fields in a class to columns in a table
## EntityManager
+ Interface that interacts with **persistence context** to perform CRUD operations, manage transactions and querying.
## Persistence Context
+ Set of entity instances that are managed by **EntityManager** .
+ Ensures that all entity instances are synchronized with database
## Criteria API
+ Platform to generate dynamic queries at runtime unlike JPQL queries which are static.
## JPQL (Java Persistence Qeury Language)
+ Query language for making queries against entities stored in database.
+ Since entities are used in making queries, it abstracts database classes, making it **database agnostic**
## Persistence Unit
+ It specifies the entity classes that are included in the persistence context and manages how these entities are configured with database

