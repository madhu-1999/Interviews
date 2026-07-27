---
tags:
  - "#oop"
---
Inheritance allows us to define a class that inherits all the methods and properties from another class. The parent/base class is the class being inherited from, and the child/derived class is the class that inherits from another class.
+ [p] Eliminates redundant code by promoting reusability across a hierarchy.
>[!example] A `Car` and a `Motorcycle` both inherit some basic properties from a parent `Vehicle` class, but they differ in implementation of say `drive()` method or the `numberOfWheels` property.
## Types of inheritance
### **Single Inheritance**
Sub class derived from **one** parent class.
![|641x319](https://media.geeksforgeeks.org/wp-content/uploads/20220728111827/1-660x329.jpg)
### **Multilevel Inheritance**
A subclass acts as a parent class for another class.
![[Pasted image 20250219160701.png]]
### **Multiple Inheritance**
**One child, multiple parents**
![[Pasted image 20250219160923.png]] 
### **Hierarchical Inheritance**
**One** class serves as **parent** class for **multiple** **child** classes.

![[Pasted image 20250219160816.png]]

### **Hybrid Inheritance**
Mix of 2 or more of above types of inheritance