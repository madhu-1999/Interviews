#java 
+ Every class, interface etc is part of a package.
+ If no package specified it is part of a default package.
+ Easy to manage, locate related groups of classes, interfaces adn other components.
+ Prevents naming conflicts. Same class say `Employee` can exist in different packages say `org.app.Payroll.Employee` and `org.app.Department.Employee`
+ Provide access protection to class instance variables, static variables and methods.
	+ Public members accessible outside package to any other package
	+ Protected members accessible within package and only to subclasses outside package.
	+ Default members accessible only within package
	+ Private members accessible only within class
+ Naming convention: reverse of web domain Ex: `com.appzen.<package-name>`
+ `package` keyword used to declare that a class in part of a package
```java
package com.app.shapes;
public class Shape{}
```
+ Access class in different package by using **fully qualified name of class**: `<package-name>.<class-name>`
```java
package com.app.Book;
import com.app.shapes.Shape;
public class Sheet{
	Shape shape;
}
```