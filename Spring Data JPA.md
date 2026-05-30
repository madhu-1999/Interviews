#java #spring
+ Built on **top of [[JPA]]**, ***NOT*** a implementation.
+ Simplifies working with JPA, through high-level abstractions and utilities.
+ Underneath, a JPA-compliant implementation like Hibernate is used to handle actual database interactions. (Default is Hibernate)
+ **Only for RDBMS**.
+ Provides out-of-the-box implementation for CRUD operations, **reducing boilerplate code.**
+ Two ways to interact 
	+ Query method DSL: Creates queries automatically, based on method naming conventions
	+ Criteria API:build complex and custom queries, without raw SQL, in a object oriented manner.
# Repositories/ Interfaces
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1MI9z0__FHIKxJdu7kC9TI4bt1RDYJBKnJ9Wuzuv3NFOqSBDsFzsOg1FMen1OTsIX5tRg_PxNVN5Kv40FZPm8HCfiaqRcFmP5H1MoN-ei4pzAVMhcXqLtAA3rQyaPqMn3FAhoLGx09FA/s1600/spring-data-jpa-diagram.png)
## Repository<T, ID extends Serializable> interface
The _Repository<T, ID extends Serializable> interface_ is a marker interface that has two purposes:
- It captures the type of the managed entity (T) and the type of the entity’s id (ID).
Book $\rightarrow$ T,  ID$\rightarrow$ISBN
```java
@Repository
public interface BookRepository extends Repsository<Book, ISBN>
```
## CrudRepository<T, ID extends Serializable> interface
+ Extends Repository<T, ID extends Serializable> interface
+ Provides CRUD operations for managed entity. Operations in diagram
## PagingAndSortingRepository<T, ID extends Serializable> interface
+ Extension of _CrudRepository_ to provide additional methods to retrieve entities using the pagination and sorting abstraction. Operations in diagram
## QueryDslPredicateExecutor interface
+ ***NOT*** a “repository interface”. It declares the methods that are used to retrieve entities from the database by using _QueryDsl_ Predicate objects.
+ Operations in diagram
## JpaRepository<T, ID extends Serializable> interface
+ JPA-specific repository interface that combines the methods declared by the common repository interfaces behind a single interface.
+ Inherits all above operations (Except QueryDSLPredicateExecutor) + operations in diagram.
+ Can write criteria queries in this
## JpaSpecificationExecutor interface
+ ***NOT*** a “repository interface”. It declares the methods that are used to retrieve entities from the database by using Specification objects that use the JPA criteria API.
# Query Generation from Method names
+ It is a query generation strategy where the invoked query is derived from the name of the query method. i.e. behind the scenes, Spring Data JPA will create SQL queries based on the query method name and execute the query for us.
```java
public interface UserRepository extends Repository<User, Long> {
  List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```
## Rules for creating method names
+ Name must start with following prefixes: `find...By` , `read...By` , `count...By`, `get...By` and `query...By`
+ To limit entries add `First` or `Top` keyword **before first By**
```java
// Ex:
findFirstByName(String name)
getTop10ByName(String name)
```
+ To get unique results add `Distinct` keyword **before first By**.
```java
//Ex:
findDistinctByName(String name)
findTop10DistinctByName(String name)
```
+ Supported Keywords
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi602W822CAPnHRW-GCibiiMwMMWCog42Bqg-0kB0ndnHjBnhUEiAxnj8nJMdyu6skagwB6OFIAYQ-9Zi8_wOjJmp7zkjTBsVWyvapizDadJaWINo7mJljWEo_Tc5YHjoiSVu7zH79zw04/s1600/spring-data-jpa-keywords1.PNG)
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhnorcak_gV7hZNr0Ow_c2mQ_Q9iAE_Ua0bzJHfDVEFhXAYnW-u6UxZDlT33G3gebw6kSieEvhXKlYigWZvlIwM2qEnk7CtkTyfJKAUOuWF56z4RIEQZaTUEAheKcULasbQ8o8VaVxLJs0/s1600/spring-data-jpa-keywords2.PNG)
+ Disadvantage:
	+ No dynamic queries
	+ Method names become long and ugly for complex queries.
	+ If method parser does not support keyword, cannot use
# Named Queries
## @NamedQuery
+ Specifies a static, named query in JPQL.
+ Query name must be unique in the persistence context.
+ Applied to a entity or mapped superclass
+ In Spring Data JPA, name must be specified as` <Entity_name>. <method-name>`.
```java
@Entity
@Table(name = "users")
@NamedQuery(name = "User.findByEmailAddress", query = "select u from User u where u.emailAddress = ?1")
public class User {
}
```
## @NamedQueries
+ Specifies multiple JPQL queries.
+ Query names must be unique in the persistence context.
+ Applied to a entity or mapped superclass
+ In Spring Data JPA, name must be specified as` <Entity_name>. <method-name>`.
```java
@Entity
@Table(name = "users")
@NamedQueries(value = {
  @NamedQuery(name = "User.findByLastname", query = "select u from User u where u.lastname = ?1") })
public class User {
}
```
##
Then, the named queries must be specified in a Spring Data JPA repository
```java
@Repository
public interface UserRepository extends JpaRepository <User, Long> {
    User findByEmailAddress(String emailAddress);

    List <User> findByLastname(String lastname);
}
```
# Native queries
+ Define the query in native SQL by losing the database platform independence i.e. query does not pass through ORM layer, directly goes to Db.
## @NamedNativeQuery
+ Specifies a static, named query in JPQL.
+ Query name must be unique in the persistence context.
+ Applied to a entity or mapped superclass
+ In Spring Data JPA, name must be specified as` <Entity_name>. <method-name>`.
```java
@Entity
@Table(name = "users")
@NamedNativeQuery(name = "User.findByEmailAddress", query = "select u from User u where u.emailAddress = ?1")
public class User {
}
```
## @NamedNativeQueries
+ Specifies multiple JPQL queries.
+ Query names must be unique in the persistence context.
+ Applied to a entity or mapped superclass
+ In Spring Data JPA, name must be specified as` <Entity_name>. <method-name>`.
```java
@Entity
@Table(name = "users")
@NamedNativeQueries(value = {
  @NamedNativeQuery(name = "User.findByLastname", query = "select u from User u where u.lastname = ?1") })
public class User {
}
```
##
Then, the named native queries must be specified in a Spring Data JPA repository
```java
@Repository
public interface UserRepository extends JpaRepository <User, Long> {
    User findByEmailAddress(String emailAddress);

    List <User> findByLastname(String lastname);
}
```
# @Query
+ Supports both **JPQL** and **SQL** queries.
+ Query specified by ***@Query*** takes precedence over all others.
+ Specified in repository
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

	@Query("select u from User u where u.emailAddress = ?1")
	User findByEmailAddress(String emailAddress);

	@Query("select u from User u where u.firstname like %?1")
	List<User> findByFirstnameEndsWith(String firstname);
}
```
+ Query can be set to native as shown
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(value = "select * from users where first_name like %?1", nativeQuery = true)
    List<User> findByFirstnameEndsWith(String firstname);

    @Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)
    User findByEmailAddress(String emailAddress);
}
```