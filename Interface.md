```table-of-contents
title: Table of Contents
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
An interface is a **blueprint of a behavior**.

+ A mechanism to achieve [[Abstraction]] and Multiple inheritance[^1] . It provides **total** abstraction.
+ By default(implicit), **variables** are **public, static, and final**.
+ By default, all the **methods** are **public** and **abstract**.
+ Defines a set of behaviors that a class can implement.
+ **Cannot** be instantiated, so **constructors** are ***NOT* allowed**.
+ Declaration:
```java
public interface A {//some code}
```

+ Interface **extends** interface, class **implements** interface.
+ A class implementing an interface **must** define all methods in an interface (except default). If not, then the class must be declared as **abstract**.
### Java 8
+ **Default methods** with default implementations can be defined:
```java
interface A {
	default void display(){
		System.out.println("hello");
	}
}

class B implements A {
	public static void main(String args[])
	{
		B b = new B();
		b.display(); // B did'nt have to implement display method.
	}
}
```

+ **Static methods** defined in an interface **cannot be inherited**, and are called directly using interface name. 
### Java 9
+ **Private methods** (Instance/static) that are accessible only in the interface, **cannot be inherited**.
+ Private methods **cannot** be abstract.

## Footnotes
[^1]: [[Inheritance#**Multiple Inheritance** **One child, multiple parents**]]
