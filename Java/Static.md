#java 
+ Keyword associated with
	+ Blocks
	+ Variables
	+ Methods
	+ Classes
+ Static methods and variables are associated with class ***NOT*** object so all instances share the same static methods and variables.
# Static methods
+ Cannot access non-static members of a class.
+ Cannot directly call non-static methods of a class. Need to create an object to invoke the method 
```java
public static void staticMethod(){
	MyClass myClass = new MyClass()
	myClass.nonStaticMethod();
}
```
+ **Can be overloaded**
+ **Cannot be overridden**, since overriden methods are resolved at runtime (runtime/dynamic polymorphism), but static methods are loaded into memory at compile time (static polymorphism).
+ Call using class name (preferred). Can be invoked using instance also. Ex: `MyClass.staticMethod()`.
# Static Variables
+ Can access directly through class name (preferred). Can be invoked using instance also Ex: `MyClass.myStaticVar`
# Static block
+ Executed once when class loaded.
+ Cannot access non-static variables and methods
```java
static int a = 10;
static int b;
static{
	b=a*4;
}
```
# Static class
+ Only nested classes can be static.
+ Can be instantiated without instantiating outer class
+ Can access only static members of outer class