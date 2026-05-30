#lld #system-design #design-patterns #java 
# Definition
The Adapter Pattern converts the interface of a class into another interface the clients expect. It lets classes work together that couldn’t otherwise because of incompatible interfaces.
# Need for Adapter Pattern
Say you’ve got an existing software system that you need to work a new vendor class library into, but the new vendor designed their interfaces differently than the last vendor.
You don’t want to solve the problem by changing your existing code (and you can’t
change the vendor’s code). So what do you do? Well, you can write a class that adapts the new vendor interface into the one you’re expecting.![[Screenshot 2025-12-21 at 4.31.26 PM.png]]
Lets say we are creating a Duck Simulator, and we want to introduce turkeys into the simulator without changing the existing code.
This can be achieved by writing a Adapter class to convert a `Turkey` instance into a `Duck` instance.
## Object Adapter
```java
public interface Turkey {
	public void gobble();
	public void fly();
}

public interface Duck {
	public void quack();
	public void fly();
}

public class TurkeyAdapter implements Duck {
	Turkey turkey;
	
	public TurkeyAdapter(Turkey turkey) {
		this.turkey = turkey;
	}
	 
	// implement all methods of Duck interface
 	public void quack() {
	 	turkey.gobble();
 	}
 	
 	public void fly() {
	 	turkey.fly();
 	}
}
```

Running this code:
```java
public class DuckTestDrive {
	public static void main(String[] args) {
		MallardDuck duck = new MallardDuck();
		WildTurkey turkey = new WildTurkey();
		Duck turkeyAdapter = new TurkeyAdapter(turkey);

		System.out.println(“\nThe Duck says...”);
		testDuck(duck);

		System.out.println(“\nThe TurkeyAdapter says...”);
		testDuck(turkeyAdapter);
	}
	
	static void testDuck(Duck duck) {
		duck.quack();
		duck.fly();
	}
}
```
## Class Adapter
Instead of composing the `Turkey` object with the `TurkeyAdapter` object. in class adapter pattern, the `TurkeyAdapter` class must extend the specific adaptee class say `WildTurkey` and the `Duck` class. 
But this is not possible in Java, since multiple inheritance is not allowed. 
## Class vs Object Adapters
| Feature          | Class Adapter (Extends `WildTurkey`) | Object Adapter (Wraps `Turkey` Interface) |
| ---------------- | ------------------------------------ | ----------------------------------------- |
| **Relationship** | **Is-a** Turkey                      | **Has-a** Turkey                          |
| **Logic Reuse**  | Uses inheritance (inherited code)    | Uses composition (calls the object)       |
| **Flexibility**  | Can only adapt that specific class   | Can adapt any class implementing `Turkey` |

# UML Diagram
## Object Adapter
![[Screenshot 2025-12-21 at 5.04.18 PM.png]]
## Class Adapter
![[Screenshot 2025-12-21 at 5.15.11 PM.png]]
