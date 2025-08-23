## Prerequisites:
+ Inheritance
+ Composition
+ SOLID
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
	+ Any components of the object (Think [[Composition]]).
7. The Hollywood Principle:  Don’t call us, we’ll call you.  This helps prevent ''dependency rot". i.e. High level components depend on low-level components. Under this principle, *only* the high-level components call the low-level components ***NOT*** vice-versa.
## Strategy design pattern
+ Pts 1, 2, 3, 4 apply
+ It is a **behavioral** design pattern that lets you define a family of algorithms, put each of them into a separate class and make their objects interchangeable.(DI)
+ ![Structure of the Strategy design pattern](https://refactoring.guru/images/patterns/diagrams/strategy/structure-indexed.png?id=ff55c5a6273cf82a5667f3cab5b605c7)
The process is as follows:
1. The **Context** maintains a reference to one of the concrete strategies and communicates with this object via **strategy** interface.
2. The **Strategy** interface is common to all concrete strategies. It declares a method, the context uses to execute the strategy.
3. **Concrete strategies** implement variations of an algorithm the context uses.
4. Context calls execution method on linked strategy object **without** knowing what type of strategy it works with.
5. The **Client** creates a specific strategy object and passes it to context, which exposes a setter to let client replace associated strategy at runtime.
+ Example: `Duck` subclass is **context**, `FlyBehavior` & `QuackBehavior` are **strategies** and their subclasses are concrete strategies. ![[Screenshot 2025-02-20 at 4.37.47 PM.png]]

## Observer Design Pattern

+ Pts 1, 2, 3, 4 apply. It is a behavioral design pattern.
+ It defines a **one-to-many** dependency between objects so that when the objects state changes, all its dependents are notified and updated automatically.
+ Visual explaining Observer Design Pattern. Subjects are **Publishers** and Observers are **Subscribers**. Subscribers can subscribe and unsubscribe anytime (dynamic/ at runtime).
![[Screenshot 2025-02-20 at 5.14.25 PM.png]]
+ The only thing the Subject knows about the (concrete) Observer is that it implements an interface, which makes it easier to change/add observers.![[Screenshot 2025-02-20 at 5.23.50 PM.png]]
+ Example: ![[Screenshot 2025-02-20 at 5.43.23 PM.png]]

### Java

Observer design pattern can be implemented by using the java.util.Observable class (Subject extends) and java.util.Observer Interface (Observer implements).

#### **How it works**
+ Observer (implementing Observer interface) can subscribe and unsubscribe to the subject by using the Observable object (think. [[Java Inheritance#Upcasting]]).
+ For subject to send notifications, first change `setChanged()` method to signify state change and then call either `notifyObservers()` or `notifyObservers(Object arg)`.
+ `notifyObservers()` is used when we want to implement a **pull** mechanism for notifications, where observers get the data themselves. `notifyObservers()` only notifies them of the change.
+ `notifyObservers(Object arg)` is used to implement the **push** mechanism for notifications. The subject sends the data object `arg` along with the state change notification to the observers.
+ `setChanged()` is implemented to control the frequency of notifications. If set to `true`, notifications are sent. For example, we may only want to notify observers if a parameter changes by *x* instead of each time it changes.

#### **Pitfalls of java.util.Observable**

+ Observable is a class not an interface, which means one has to subclass it. This limits the Subject class from extending another class and violates *Program to an interface, not implementation.*
+ `setChanged()` method is **protected**, which means to access it, Observable must be extended. This violates *Favor composition over inheritance*.
+ If this goes against the requirements of your application, you can create your own implementation of the Subject Interface.

## Decorator Pattern

+ It is a structural design pattern.
+ Decorator pattern attaches new behaviors to objects by wrapping them in special wrapper objects called **decorators**, that contain the behaviors.
![[image.png]]
+ Decorators have **same supertype** as the objects they decorate, which means that we can pass the decorated object instead of the original one.
+ An object can be decorated by as many decorators as needed.
+ It allows us to extend an object's behavior without creating a subclass.
+ Decorators can be added or removed at runtime.
+ They add behaviors either **before** or **after** delegating responsibility to the next class. In the image above, it is after.
+ The decorators are all **on a stack** with the **outermost** decorator being on **bottom** and the **component** being wrapped on **top**.
+ `Component` here, is an abstract class. Remember, the `Decorator` class extends `Component` *AND* has a `Component` variable (Composition).![[Screenshot 2025-02-20 at 10.18.27 PM.png]]
+ **Disadvantages**
	+ It adds a lot of small classes to the code.
	+ They can introduce problems if some code relies on the concrete type instead of the abstract one.
	+ Behavior depends on the **order** of decorators.

## Factory Pattern
https://www.youtube.com/watch?v=7g9S371qzwM&ab_channel=Concept%26%26Coding-byShrayansh
+ It is a **creational** design pattern.
+ **new** is used in Java to instantiate an object, but when we code to an interface we create a reference to the interface. Ex: `Animal animal = new Dog()` to make our code more flexible to change at runtime through dynamic polymorphism. This introduces a new problem, the code to assign concrete types at runtime starts looking like so: ``````
```java
if(bark) 
	animal = new Dog()
else if(meow)
	animal = new Cat()
```
This means that everyone we add/remove a subtype this code will have to be modified. This violates **Open-closed principle** in [[SOLID]].
+ We must encapsulate object creation using the Factory design pattern.
+ Factory pattern defines an interface for creating an object, but lets subclasses decide which class to instantiate i.e. the superclass does not know which subclass it is implementing. ![[Screenshot 2025-02-21 at 1.09.34 PM.png]]
+ Abstract Factory pattern provides an interface for creating **families** of related/dependent objects without specifying their concrete classes.

## Singleton

+ It is a **creational** pattern, used when we want **only one** instance of a class, for example, a logging object, registry object, thread pool.
+ The Singleton Pattern also provides a global access point to that instance. Here it is getInstance(). The constructor is private so only Singleton class can create its object.
```java
public class Singleton { 
	private static Singleton uniqueInstance = new Singleton(); 
	private Singleton() {} 
	public static Singleton getInstance() { return uniqueInstance; } 
}
```
+ Be careful if you are using multiple class loaders; this could defeat the Singleton implementation and result in multiple instances.

## Command Pattern

+ is a **behavioral** design pattern that turns a request into a stand-alone object that contains all information about the request. This transformation lets you pass requests as a method arguments, delay or queue a request’s execution, and support undoable operations.
![[Screenshot 2025-02-24 at 10.36.42 PM.png]]
+  Classes that invoke operations can be decoupled from classes that perform these operations.
-  New commands can be introduced into the app without breaking existing client code. (Open/closed principle)
-  You can implement undo/redo.
-  You can implement deferred execution of operations (queuing requests).
-  You can assemble a set of simple commands into a complex one.(Executing a command that in turn executes multiple commands)

## Adapter Pattern
+ The Adapter Pattern converts the interface of a class into another interface the clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.![[Screenshot 2025-02-25 at 2.03.57 PM.png]]
+ Object Adapter ![[Screenshot 2025-02-25 at 2.07.37 PM.png]]
+ Class Adapter![[Screenshot 2025-02-25 at 2.08.04 PM.png]]

## Facade Pattern
+ The Facade Pattern provides a unified interface to a set of interfaces in a subsytem. Facade defi nes a higherlevel interface that makes the subsystem easier to use.
+ Facades don’t “encapsulate” the subsystem classes; they merely provide a simplified interface to their functionality. The subsystem classes still remain available for direct use by clients that need to use more specific interfaces.
+ The pattern certainly allows for any number of facades to be created for a given subsystem
+ A facade is free to add its own “smarts” in addition to making use of the subsystem.
+ The Facade Pattern also allows you to decouple your client implementation from any one subsystem. Let’s say for instance that you get a big raise and decide to upgrade your home theater to all new components that have different interfaces. Well, if you coded your client to the facade rather than the subsystem, your client code doesn’t need to change, just the facade.
+ The difference between the two is not in terms of how many classes they “wrap,” it is in their intent. The intent of the Adapter Pattern is to alter an interface so that it matches one a client is expecting. The intent of the Facade Pattern is to provide a simplified interface to a subsystem.
+ ![[Screenshot 2025-02-25 at 2.37.57 PM.png]]

## Template Pattern
+ The Template Method defines the steps of an algorithm and allows subclasses to provide the implementation for one or more steps. Algorithm is "encapsulated".![[Screenshot 2025-02-25 at 2.54.39 PM.png]]
+ A hook is a method that is declared in the abstract class, but only given an empty or default implementation. This gives subclasses the ability to “hook into” the algorithm at various points, if they wish; a subclass is also free to ignore the hook.
+ Use abstract methods when your subclass *MUST* provide an implementation of the method or step in the algorithm. Use hooks when that part of the algorithm is optional.
+ To prevent subclasses from changing the algorithm in the template method, declare the template method as final.
+ The Template Method Pattern gives us an important technique for code reuse.
## Reference
1. Head First Design Patterns (Great Book)