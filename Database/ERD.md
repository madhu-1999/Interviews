#database 
+ Stands for Entity Relationship Diagram
+ Represent logical structure of a database.

# Symbol Overview
![Symbols used in ER Diagram](https://media.geeksforgeeks.org/wp-content/uploads/20230428115454/Introduction-to-ER-Model-2-768.webp)

# Component Overview
![Components of ER Diagram](https://media.geeksforgeeks.org/wp-content/uploads/20230428090323/Introduction-to-ER-Model-1-768.webp)

# Entity
Any object with physical or conceptual existence.

## Strong Entity
+ It has a **primary key** attribute. 
+ **Doesn't depend** on another entity.
+ Represented by **single rectangle**.

## Weak Entity
+ **No key** attribute.
+ **Depends** on a strong entity i.e. cannot exist without strong entity
+ Represented by **double rectangle**.

## Example
An employee has dependents (parents, children, spouse). The dependents cannot exist in employee db without related employee data.
`Employee` is strong entity, `Dependents` is weak entity.
Double diamond is for weak relationship.
![Strong Entity and Weak Entity](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20210219121849/12310.png)
# Attributes
+ Properties(columns) that define an entity.
+ Represented by an **oval**.![Attribute](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-2.png)
## Key attribute
Primary key is represented as an **oval with underline**.
![Key Attribute](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-3.png)
## Composite attribute
Attribute composed of many other attributes.![Composite Attribute](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-4.png)
## Multivalued attribute
+ Attribute which will contain more than one value.
+ Represented by a **double oval**.![Multivalued Attribute](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-5.png)
## Derived attribute
+ Attribute derived from another attribute. Ex: Age derived from DOB.
+ Represented by **dotted oval**. 
 + ![Derived Attribute](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-6.png)
## Example

![Entity And Attribute](https://media.geeksforgeeks.org/wp-content/uploads/20240618023839/studn-dbms.png)
# Relationships
+ Represented by a **diamond** connecting entities with lines.![Entity-Relationship Set](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-8.png)
## Degrees
### **Unary**
***One*** entity participating in a relation. Ex: A person is married to (another) one person.
![Unary Relationship](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-10.png)
### **Binary**
***Two*** entities participating in a relationship.![Binary Relationship](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-8.png)
### **N-ary**
***N*** entities participating in a relationship.

## Cardinality
No of instances of an entity that can participate in a relationship.

### **One-to-one**
Only ***ONE*** instance of each entity participates in a relationship.![onetoone](https://media.geeksforgeeks.org/wp-content/uploads/20230920133529/onetoone.jpg)
### **One-to-many**
![one to many](https://media.geeksforgeeks.org/wp-content/uploads/20230920133617/onetomany.jpg)
### **Many-to-one**
![manytoone](https://media.geeksforgeeks.org/wp-content/uploads/20230920133703/manytoone.jpg)
### **Many-to-many**
![manytomany](https://media.geeksforgeeks.org/wp-content/uploads/20230920133746/manytomany.jpg)
## Participation Constraint

### **Total Participation**
+ Every entity in the entity set must participate in the relationship.
+ Ex: Every `Student` *MUST* enroll in a `Course`.
+ Represented by a **double line**.
### **Partial Participation**
+ Entities in an entity set may or may not participate in the relationship.
+ Ex: A `Course` may or not have a `Student` enrolled.
+ Represented by **single line**.
### Example
![Total Participation and Partial Participation](https://media.geeksforgeeks.org/wp-content/uploads/Database-Management-System-ER-Model-18.png)