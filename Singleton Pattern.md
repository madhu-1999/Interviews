#lld #system-design #design-patterns #java 
# Definition
The Singleton pattern ensures that there exists **only one instance** of a class, and provides a global point of access to it.
# Class Diagram
![[Screenshot 2025-09-28 at 3.02.28 PM.png]]
# Java Implementation
```java
public class Singleton {
	private static Singleton uniqueInstance;
	
	private Singleton() {}
	
	public static synchronized Singleton getInstance() {
		if(uniqueInstance == null) {
			uniqueInstance =  new Singleton();
		}
		return uniqueInstance;
	}
}
```

The constructor is declared `private`  so that a new instance of `Singleton` class cannot be created by other classes.
But how do other classes request an instance of the `Singleton` class if constructor is inaccessible?

That is where the static method `getInstance()` comes in. 
Other classes cannot request a new instance using `Singleton s = new Singleton()` , but they can do so using the static method as `Singleton s = Singleton.getInstance()`.
The static method ensures that only one instance is created (with lazy initialization i.e. only created when requested). Since` getInstance()` is a static method `uniqueInstance` has to be static variable as static methods cannot access non-static class members.
But what happens when two threads try to request an instance at the same time (race condition)?

The `synchronized` keyword prevents race conditions and ensures only one thread can access the method at a time. 
However, there is a performance overhead, since method is synchronized each time it is called i.e. even after instance is created when there is no need for synchronization.
## Double checked Locking
This method reduces performance overhead by using `synchronized` block. This way synchronization happens only when `uniqueInstance == null`.

```java
public class Singleton {
	private static volatile Singleton uniqueInstance;
	
	private Singleton() {}
	
	public static Singleton getInstance() {
		if(uniqueInstance == null) {
			synchronized {
				if(uniqueInstance == null) {
					uniqueInstance =  new Singleton();
				}
			}
		}
		return uniqueInstance;
	}
}
```

The `volatile` keyword ensures that multiple threads accessing `uniqueInstance` will always read from main memory and not cache, thus always getting latest value.

The first `uniqueInstance == null` check ensures that if instance has already been created, thread doesn't enter synchronized block unnecessarily.

Imagine two threads A and B simultaneously process first null check and try to enter the synchronized block. Thread A receives the lock, passes the second null check and creates an instance of the `Singleton` class. Then thread B receives a lock on the synchronized block, fails the second `uniqueInstance == null` check and leaves the block. Without the second check, thread B would have created a second instance of the `Singleton` class.
# Singleton vs Global Variable
Just like a global variable, the Singleton pattern lets you access some object from anywhere in the program. However, it also **protects that instance from being overwritten** by other code.

A global variable is initialized whether it is used or not (eager initialization) whereas a Singleton instance is created only when requested (lazy initialization).
# Disadvantages
+ Violates [[SOLID#**S**ingle Responsibility Principle|Single Responsibility Principle]] since a Singleton class has two distinct responsibilities:
	+ Providing a single instance of the class
	+ Primary business logic
+  It may be difficult to unit test the client code of the Singleton because it is difficult to mock them, since the constructor of the singleton class is private and overriding static methods is impossible.
# References
https://refactoring.guru/design-patterns/singleton
Head First Design Patterns (Java)