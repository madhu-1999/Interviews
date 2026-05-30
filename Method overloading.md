#java 
## Java
+ A method is overloaded if it has **same name** but **different type, number or sequence of parameters**.
+ Called **compile time** polymorphism, since method binding is done at compile time.

> Sequence of parameter overloading:
```java
public void studentId(String name, int rollno);
public void studentId(int rollno, String name); //overloaded
```

+ If **return type** is **different**, it is a **different** function ***NOT* overloaded** function.
+ Main function can be overloaded, but there will be a runtime error if there is no function with the method signature
```java
public static void main (String args[])
```
+ **Static** functions can be **overloaded**, but **overloaded** function **must also be static**.
	+ A class cannot have a static and non-static function with same name and method signature.
	+ This is because, both methods can be accessed using object of class and compiler won't be able to decide which function to use.
	+ Overloaded static functions must also be static to distinguish if it is overloaded function of the static or non - static function.
