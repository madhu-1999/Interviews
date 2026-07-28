
## Prerequisites:
+ [Inheritance](definitions/Inheritance.md)
+ [Composition](Composition.md)
+ [SOLID](SOLID.md)
## General Design principles
1. Identify aspects of your application that **vary** and separate (encapsulate) them so that it is easier to alter/extend without affecting other parts.
2. Program to an interface, not an implementation (Dependency injection).
3. Composition over inheritance.
4. Strive for loosely coupled designs between objects that interact with each other.
5. Entities should be open for extension, closed for modification.
6. Principle of least knowledge: We should only invoke methods that belong to:
	+ Object itself
	+ Object passed as parameter to the method
	+ Any object the method creates/instantiates
	+ Any components of the object (Think [Composition](Composition.md)).
7. The Hollywood Principle:  Don’t call us, we’ll call you.  This helps prevent ''dependency rot".Under this principle, *only* the high-level components call the low-level components ***NOT*** vice-versa.
## Adapter Pattern
+ The Adapter Pattern converts the interface of a class into another interface the clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.![Screenshot 2025-02-25 at 2.03.57 PM](Assets/Screenshot%202025-02-25%20at%202.03.57%20PM.png)
+ Object Adapter ![Screenshot 2025-02-25 at 2.07.37 PM](Assets/Screenshot%202025-02-25%20at%202.07.37%20PM.png)
+ Class Adapter![Screenshot 2025-02-25 at 2.08.04 PM](Assets/Screenshot%202025-02-25%20at%202.08.04%20PM.png)

## Facade Pattern
+ The Facade Pattern provides a unified interface to a set of interfaces in a subsytem. Facade defi nes a higherlevel interface that makes the subsystem easier to use.
+ Facades don’t “encapsulate” the subsystem classes; they merely provide a simplified interface to their functionality. The subsystem classes still remain available for direct use by clients that need to use more specific interfaces.
+ The pattern certainly allows for any number of facades to be created for a given subsystem
+ A facade is free to add its own “smarts” in addition to making use of the subsystem.
+ The Facade Pattern also allows you to decouple your client implementation from any one subsystem. Let’s say for instance that you get a big raise and decide to upgrade your home theater to all new components that have different interfaces. Well, if you coded your client to the facade rather than the subsystem, your client code doesn’t need to change, just the facade.
+ The difference between the two is not in terms of how many classes they “wrap,” it is in their intent. The intent of the Adapter Pattern is to alter an interface so that it matches one a client is expecting. The intent of the Facade Pattern is to provide a simplified interface to a subsystem.
+ ![Screenshot 2025-02-25 at 2.37.57 PM](Assets/Screenshot%202025-02-25%20at%202.37.57%20PM.png)

## Template Pattern
+ The Template Method defines the steps of an algorithm and allows subclasses to provide the implementation for one or more steps. Algorithm is "encapsulated".![Screenshot 2025-02-25 at 2.54.39 PM](Assets/Screenshot%202025-02-25%20at%202.54.39%20PM.png)
+ A hook is a method that is declared in the abstract class, but only given an empty or default implementation. This gives subclasses the ability to “hook into” the algorithm at various points, if they wish; a subclass is also free to ignore the hook.
+ Use abstract methods when your subclass *MUST* provide an implementation of the method or step in the algorithm. Use hooks when that part of the algorithm is optional.
+ To prevent subclasses from changing the algorithm in the template method, declare the template method as final.
+ The Template Method Pattern gives us an important technique for code reuse.
## Reference
1. Head First Design Patterns (Great Book)