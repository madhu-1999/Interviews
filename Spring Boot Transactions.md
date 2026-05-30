#java #spring #aop
+ Helps to achieve [[ACID|ACID properties]].
# @Transactional Overview
+ Indicates that all db interactions should be considered as one transaction (on method level).
+ If class is annotated with @Transactional, all **public** methods are implicitly annotated as @Transactional.
# Enabling Transactions
+ ***@EnableTransactionManagement*** in config file, but Spring boot internally configures in ***@SpringBootApplication*** through ***@EnableAutoConfiguration***.
## Internal Working of @Transactional
+ Uses [[Spring AOP| AOP]] internally to achieve transaction management
+ Pointcut expression is used to search for @Transactional annotated methods.
+ When found, "Around" advise is executed
# Transaction Managers
![[Image.jpg|softwareengineer | Shrayansh Jain]]
## Declarative Transaction Management (Preferred)
+ Transaction management through annotations (@Transactional).
+ Spring Boot chooses appropriate transaction manager at runtime
+ Can explicitly create one and tell spring to use it
## Programmatic Transaction Management
+ Handled through code using `TransactionManager` or `TransactionTemplate`
+ Flexible but difficult to manage
# Transaction Propagation
+ Refers to transaction behavior when a method annotated with @Transactional is called by or calls another method which may/may not be annotated with @Transactional.
## Required
+ `@Transactional(propagation=Propagation.REQUIRED)`
+ If calling method/called method is not annotated with @Transactional then new transaction is started.
+ If calling method is annotated with @Transactional then called method becomes part of same transaction as calling method.
+ This is **default** propagation level.
## Requires New
+ `@Transactional(propagation=Propagation.REQUIRES_NEW)`
+ If calling method/called method is not annotated with @Transactional then new transaction is started.
+ If calling method is annotated with @Transactional even then called method starts a new transaction
## Mandatory
+ `@Transactional(propagation=Propagation.MANDATORY)`
+ If calling method/called method is not annotated with @Transactional then `IllegalTransactionStateException` is thrown
+ If calling method is annotated with @Transactional then called method becomes part of same transaction as calling method.
## Nested
+ `@Transactional(propagation=Propagation.NESTED)`
+ If calling method/called method is not annotated with @Transactional then new transaction is started.
+ If calling method is annotated with @Transactional then called method becomes part of same transaction as calling method in a nested manner i.e. a savepoint is created up till method call, then continue in same transaction. Allows for **partial rollback** (until savepoint).
## Supports
+ `@Transactional(propagation=Propagation.SUPPORTS)`
+ If calling method/called method is not annotated with @Transactional then ***NO*** new transaction is started. i.e. no transaction supported
+ If calling method is annotated with @Transactional then called method becomes part of same transaction as calling method.
## Not supported
+ `@Transactional(propagation=Propagation.NOT_SUPPORTED)`
+ If calling method is annotated with @Transactional then called method ***DOES NOT*** become part of the transaction ***NOR*** does it start a new transaction
## Never
`@Transactional(propagation=Propagation.NEVER)`
+ If calling method is annotated with @Transactional then called method throws a `IllegalTransactionStateException`.
# Isolation Levels
+ Transaction behavior when concurrent transactions are running and how they affect it each other. 
+ Refer [[ACID#Transaction Anomalies|Transaction Anomalies]] for problems that occur for concurrent transactions.
+ Refer [[ACID#Isolation Levels|Isolation Levels]] for transaction isolation levels
## DEFAULT
+ Assumes default isolation level of underlying db.
## READ_UNCOMMITTED
+ Dirty reads, non-repeatable read and phantom reads can occur.
+ Used when only reads are happening
## READ_COMMITTED
+ Dirty reads cannot occur
+ Non-repeatable reads and phantom reads can occur.
## REPEATABLE_READ
+ Dirty reads and non-repeatable reads cannot occur
+ Phantom reads can occur.
## SERIALIZABLE
+ Dirty reads, non-repeatable read and phantom reads cannot occur.

