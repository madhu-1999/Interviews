#java #spring 

![Spring-Framework_](https://media.geeksforgeeks.org/wp-content/uploads/20250226122317588619/Spring-Framework_.webp)
# Core container

# Data Access/ Integration
# Web Layer
# AOP
# Instrumentation
# Test

# Layered Architecture
+ This pertains to architecture of application being created. ***NOT***  how Spring works.
+ General flow of program and parts it is divided into.

![Introduction to spring boot](https://cdn.hashnode.com/res/hashnode/image/upload/v1725011770380/ed59cd17-108d-4216-a8ae-6901a086c6eb.png?auto=compress,format&format=webp)
## Presentation Layer
+ Corresponds to `Controller Layer` in diagram.
+ Handles HTTP requests/response, authentication/authorization i.e. endpoint in REST
+ @Controller, @RestController annotation
## Business Layer
+ Corresponds to `Service Layer` in diagram.
+ Business Logic is written here
+ @Service annotation
## Persistence Layer
+ Corresponds to `Repository Layer` in diagram.
+ Handles interaction b/w code and db. (CRUD ops)
+ Database it interacts with is abstraction, could be any db (SQL/NoSQL).
+ @Repository annotation
## Database Layer
+ Handles db connection, management instance of connection.
+ Concretely connects to any db (SQL/NoSQL) and provides an abstraction to persistence layer which it interacts with.

## Other Terminology
### **Data Transfer Object** (DTO)
+ HTTP request/ response POJO
+ Passed between controller and service layers
### **Utility**
+ Helper methods commonly used across classes in service/ repository layer
### **Entity**
+ Database table POJO
+ @Entity annotation
### **Configuration**
+ All configs in `application.properties` file
+ @Configuration annotation
+ Ex: timeout values or environment variables.

