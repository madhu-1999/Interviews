#system-design #lld #design-patterns #java 
# Definition
The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable i.e. dynamically changeable, independent of the client that uses them.
# Need for Strategy Pattern
Imagine you have a `Duck` class ([[Abstract classes|abstract]]) from which all types of ducks inherit as shown:
![[Pasted image 20250930145920.png]]
`display()` is an abstract method which subclasses implement. All other methods are inherited from `Duck` class.

What happens if we create `WoodenDuck` as a subclass of `Duck` which can neither _quack_ nor _fly_. We are forced to override the `quack()` and `fly()` methods to not implement the behaviors. 

```java
public class WoodenDuck extends Duck {
	public void fly() {
		System.out.println("Cannot fly");
	}
	
	public void quack() {
		System.out.println("Cannot quack");
	}
	
	public void display() {
		System.out.println("Wooden Duck");
	}
}
```

This violates [[SOLID#**I**nterface Segregation Principle (ISP)|Interface Segregation Principle]]. `WoodenDuck` is forced to implement behaviors it does not use.

Using the design principle: ___Identify parts of your application that changes, and encapsulate it so that it is easier to alter/extend without affecting other parts___, we find that `fly()` and `quack()` behaviors change.
We can encapsulate them by creating `Flyable` and `Quackable` interfaces as shown:
![[Pasted image 20250929231635.png]]
This design follows **Interface Segregation Principle**. Subclasses only implement the behaviors they use.
The downside is that all classes implementing `quack()` or `fly()` must now write their own implementations. (could make them [[Interface|default methods]] to inherit same behavior).

If say a `MallardDuck` instance, loses its voice and cannot quack anymore, under the current design we cannot change the quacking behavior at runtime. This is where Strategy pattern helps.
# Strategy Pattern
Instead of letting each `Duck` subclass implement their own `fly()` and `quack()` behaviors and creating duplicate code, we can create a set of classes for all _flying_ behaviors (`FlyWithWings`, `FlyNoWay` etc..) and all _quacking_ behaviors (`Quack`, `Mute` etc..)
![[Screenshot 2025-09-29 at 11.29.41 PM.png]]
Adding a new _flying_ or _quacking_ behavior becomes easy. We can simply implement the `FlyBehavior` or `QuackBehavior` interface. Follows [[SOLID#**O**pen for extension, closed for modification|open/closed principle]].

Going back to the [[#Definition]], we have defined a family of **algorithms** and encapsulated them (`FlyBehavior` and `QuackBehavior`) until now. We just have to make them interchangeable now.
But first lets see how the `Duck` subclasses will use these algorithms.

```java hl:2-3
public abstract class Duck {
	FlyBehavior flyBehavior;
	QuackBehavior quackBehavior;
	
	public void performFly() {
		flyBehavior.fly();
	}
	
	public void performQuack() {
		quackBehavior.quack();
	}
	//other methods
	protected void abstract display();
}
```

Notice that we are [[Composition|composing]] `FlyBehavior` and `QuackBehavior` with `Duck` instead of inheriting it. This is in accordance with the design principle : ___Composition over inheritance___.
The choice of concrete implementation of  `FlyBehavior` and `QuackBehavior` is delegated to the subclasses. 

```java hl:9-15
public class MallardDuck extends Duck {
	//[[Core Principles#**Constructor injection**|Constructor Injection]] 
	public MallardDuck(FlyBehavior flyBehavior, QuackBehavior quackBehavior) {
		this.flyBehavior = flyBehavior;
		this.quackBehavior = quackBehavior;
	}
	
	//[[Core Principles#**Setter Injection**|Setter Injection]]
	public void setFlyBehavior(FlyBehavior flyBehavior) {
		this.flyBehavior = flyBehavior;
	}
	
	public void setQuackBehavior(QuackBehavior quackBehavior) {
		this.quackBehavior = quackBehavior
	}
	
	public void display() {  
		System.out.println(“I’m a real Mallard duck”);
	} 
}
```
Here, we used constructor injection to ensure that `Duck` subclasses are not dependent on concrete implementations of `FlyBehavior` and `QuackBehavior` on instantiation. Follows [[SOLID#**D**ependency Injection]|Dependency Inversion Principle]]. 
The concrete implementations are injected as follows:

```java
public static void main(String args[]) {
	FlyBehavior flyBehavior = new FlyWithWings();
	QuackBehavior quackBehavior = new Quack();
	Duck duck = new MallardDuck(flybehavior, quackBehavior);
}
```

The setter methods are used to change the `FlyBehavior` and `QuackBehavior`  subtypes at runtime.

```java
public static void main(String args[]) {
	FlyBehavior flyBehavior = new FlyWithWings();
	QuackBehavior quackBehavior = new Quack();

	Duck duck = new MallardDuck(flybehavior, quackBehavior);
	duck.performQuack(); // Duck quacks
	
	quackBehavior = new MuteQuack();
	duck.setQuackBehavior(quackBehavior);
	duck.performQuack(); // Duck is mute
}
```

# UML Class Diagram
 ![Structure of the Strategy design pattern](https://refactoring.guru/images/patterns/diagrams/strategy/structure-indexed.png?id=ff55c5a6273cf82a5667f3cab5b605c7)
 
 `Duck` subclass is **context**, `FlyBehavior` & `QuackBehavior` are **strategies** and their subclasses are **concrete strategies**. 
# Disadvantages
+ Clients must be aware of the differences between strategies to be able to select a proper one.
# References
+ Head First Design Patterns
+ https://refactoring.guru/design-patterns/strategy