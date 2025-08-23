SOLID is a set of five design principles that make code more modular, scalable and maintainable.

## **S**ingle Responsibility Principle

+ This principle states that a class should have only one responsibility.
+ Ex: Bad Example (Violating SRP)
```java
class ReportGenerator{
	public Report generateReport() {
		//some code
	}
	public void saveReport(Report report) {
		//some code
	}
}
```
This violates SRP since the `ReportGenerator` class is also saving files, which is not its responsibility.

Good Example
```java
class ReportGenerator{
	public Report generateReport() {
		//some code
	}
}

class ReportSaver {
	public void saveReport(Report report) {
		//some code
	}
}
```

## **O**pen for extension, closed for modification

+ This principle states that new functionality should be added to entities without modifying **existing** code.
+ Bad Example:
```java
class ReportGenerator{
	public Report generateReport(String reportType) {
		if(reportType.equals("Finance")){
			//some code
		} else if(reportType.equals("Employee")) {
			//some code
		}
	}
}
```
If we now, want to generate a report for Orders, then we have to change existing code. 
Instead, we should create a **base class/ [[Interface]]** that others can inherit and implement as shown.
```java
interface ReportGenerator {
	public Report generateReport();
}

class FinanceReportGenerator implements ReportGenerator{
	public Report generateReport() {
		//Generate finance report
	}
}

class EmployeeReportGenerator implements ReportGenerator {
	public Report generateReport() {
		//Generate Employee report
	}
}
```

## **L**iskov Substitution Principle (LSP)

+ This principle states that a **child** class should ***NOT*** change behavior of parent class, thus a child class **can be a substitute** for its parent class.
+ Bad example:
```java
public class Bird {
	void fly() {
		//Some code
	}
	void eat() {
		// Some code
	}
}

public class Penguin extends Bird throws Exception{
	void fly() {
		throw new UnsupportedOperationException("cannot fly");
	}
	
	void eat() {
		// Some code
	}
}
```
Since a `Penguin` cannot fly even though it is a bird(throws exception) , it is violating the behavior underlined in the `Bird` class, therefore cannot be substituted for `Bird`. 
Instead, we can create another class `FlyingBird` that extends `Bird`, which other flying birds can extend.
```java
class Bird {
	void eat() {
		// Some code
	}
}

class FlyingBird extends Bird {
	void fly() {
		//Some code
	}
	void eat() {
		// Some code
	}
}

class Sparrow extends FlyingBird{//eat and fly}
class Penguin extends Bird{// eat}
```
## **I**nterface Segregation Principle (ISP)

+ This principle states that a subclass should not be forced to implement behaviors it does not use.
+ Bad example:
```java
public class Bird {
	void fly() {
		//Some code
	}
	void eat() {
		// Some code
	}
}

public class Penguin extends Bird {
	void fly() {
		System.out.println("Cannot fly");
	}
	void eat() {//some code}
}
```
Notice, this does ***NOT*** violate LSP, since `Penguin` class is performing alternate action instead of not supporting the behavior.[^1]
This however, violates ISP, since `Penguin` is forced to implement `fly()` method, even though it cannot fly. The fix is same as example for LSP.
## **D**ependency Injection

+ High level modules should not depend on low-level modules, but on abstractions.
+ Bad Example:
```java
class WiredKeyboard {
	void connect(){}
}

class Computer {
	private WiredKeyboard keyboard;
}
```
The problem is that `Computer` has a **dependency** on `WiredKeyboard` (a low-level/concrete module). If we want to connect to a `WirelessKeyboard` for example, the `Computer` class will have to be modified.
Instead, we should use an **abstraction** for keyboard, allowing the subtypes `WiredKeyboard` and `WirelessKeyboard` to be substituted for its parent type `Keyboard` (LSP). 
When parent class is a dependency and subclass substitutes for it, it is called **dependency injection**.
```java
interface Keyboard {
	void connect();
}

class WiredKeyboard implements Keyboard {
	void connect(){}
}

class WirelessKeyboard implements Keyboard {
	void connect(){}
}

class Computer {
	private Keyboard keyboard;
	
	Computer(Keyboard keyboard) {
		this.keyboard = keyboard;
	}
}

public class Test { 
	public static void main(String[] args) {
		Computer c1 = new Computer(new WiredKeyboard());
		Computer c2 = new Computer(new WirelessKeyboard());
	} 
}
```


[^1]: [[Java Inheritance#Upcasting]]
