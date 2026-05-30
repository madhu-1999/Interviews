```table-of-contents
title: Table of Contents
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
+ Java supports all inheritance types except **multiple**.
+ **Multiple inheritance**[^1] is supported through [[Interface]] or [[Composition]], all **others** are supported through **classes** (can use interface also). Ex: `class C implements A, B`
+ **extends** keyword is used to inherit a class. Ex: `class B extends A`.
+ All classes by **default**, inherit the **Object** class

## What can subclass do?
+ It inherits only the public, protected fields/methods of parent class. **Cannot inherit private fields/methods**.
+ Can add new fields and methods.
+ Can override parent class methods, using [[Method overriding]]
+ Can implement static methods of superclass, which results in [[Method overriding#Method Hiding vs Method Overriding|method hiding]]. i.e. the parent class static method is inaccessible through child class.
+ Subclass constructor invokes superclass constructor implicitly, but can be invoked explicitly using **super** keyword.

## Upcasting

```java
Parent p = new Child();
```

+ Typecasting **child** object to **parent** object.
+ Can access **parent** class **fields & non-overriden methods** with **public/protected** access. 
+ **Cannot** access **child** class **unique methods** or **fields**.
+ **Can** access **child overriden** methods.
+ Can be done **implicitly**.
+ Allows to write more generic code.

## Downcasting

```java
Parent p = new Parent();
Child c = (Child) p;
```

+ Must be done **explicitly**.
+ Typecast **parent** object to **child** object.
+ Can access **parent** class **fields/ non-overriden** methods with **public/protected** access.
+ **Can** access **child overriden** methods.
+ **Can** access **child** class **methods/fields**.
+ If **parent** class **field** is **overriden**, it has **access** to **child** class **field**.

[^1]: [[Inheritance#**Multiple Inheritance** **One child, multiple parents**]]
