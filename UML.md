Way to visualize a software system design.

```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Class diagram
## Class
![[Screenshot 2025-02-25 at 9.23.59 PM.png]]
+ **Name** (top compartment): class name
+ **Attributes** (middle): Properties or data associated with the class.
+ **Methods** (bottom) : Actions or methods associated with the class.
+ **+** : Public method
+ **-** : Private method
+ **#**: Protected method
+ **~**: Package

## Attributes
+ Syntax:
```
<visibility> <name> : <type> [multiplicity] = <defaultValue>
``` 
Multiplicity: indicates how many instances of type allowed.

## Methods
+ Syntax:
```
<visibility> <name>(parameters): returnType
```
Params are specified as ``name: type``

## Interface
![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff5a9233a-2083-4799-ba73-3fc57caeaead_834x448.png)

## Abstract Class
![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fdeed8e70-5b57-446f-a53f-f474e86b5c98_932x528.png)

## Enum
![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F4e737467-b184-4e3b-b8dc-711ac7840b50_644x520.png)

## Relationships

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0608aeaf-d32c-41e9-b7fa-6c105c27fab9_1032x648.png)

### **Association**
+ "Uses-a" relationship, when two objects interact. 
+ Can be one-to-one, one-to-many or many-to-many.
+ No ownership, both objects **can exist independently.**

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F662eb8e0-7697-4a1c-8a2a-137e0d6879e9_1234x1380.png)

### **Aggregation**
+ "Has-a" relationship between interacting objects where one class **contains** the other.
+ Both objects **can exist independently**.
+ Car **has a** engine

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F880fe7ba-9935-4d35-857e-b2682d3ac093_1554x486.png)

### **Composition**
+ Strong "has-a" relationship between interacting objects where one class **contains** the other.
+ Contained object **cannot exist independently**.
+ House **has a** room

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ffc9ef308-2a5e-4f8e-a61b-5835d6231b07_1552x376.png)

### **Inheritance**
+ **Is-a** relationship.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff92cd6a8-9454-4db5-8545-6b1f6c9ef1ff_1744x1186.png)

### **Implementation**
+ Relationship between class and interface.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F2b28dc2c-9bbe-4358-925e-5819bb81d722_1744x1234.png)

### **Dependency**
+ "Uses-a" relationship where change in one class affects the **other**.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1990f218-1fdd-45f5-8e23-62bf2dbcbb40_1956x456.png)

# Use case Diagram

![Use Case Diagram at a glance](https://cdn-images.visual-paradigm.com/guide/uml/what-is-use-case-diagram/02-use-case-diagram-annotated.png)
- The participation of an actor in a use case is shown by connecting an actor to a use case by a solid link.
- Actors may be connected to use cases by associations, indicating that the actor and the use case communicate with one another using messages.
- The system boundary is potentially the entire system as defined in the requirements document.
- For large and complex systems, each module may be the system boundary.

## Relationships

### **Extends**

![Use Case Diagram Notation - Extend](https://cdn-images.visual-paradigm.com/guide/uml/what-is-use-case-diagram/07-use-case-diagram-notation-extend.png)
Indicates "Invalid Password" use case may include behavior specified in base use case "Login Account".

### **Include**

![Use Case Diagram Notation - Include](https://cdn-images.visual-paradigm.com/guide/uml/what-is-use-case-diagram/08-use-case-diagram-notation-include.png)
- A uses relationship from base use case (Place order) to child use case (Login) indicates that an instance of the base use case will include the behavior as specified in the child use case.

### **Generalization**

![Use Case Diagram Notation - Generalization](https://cdn-images.visual-paradigm.com/guide/uml/what-is-use-case-diagram/08-use-case-diagram-notation-generalization.png)
- A generalization relationship is a parent-child relationship between use cases.
- The child use case is connected at the base of the arrow. The tip of the arrow is connected to the parent use case.

# Sequence Diagrams

They capture high-level interactions between user of the system and the system, between the system and other systems, or between subsystems (sometimes known as system sequence diagrams).

