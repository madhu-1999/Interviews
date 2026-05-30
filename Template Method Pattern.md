#lld #java #system-design #design-patterns 
# Definition
The Template Method Pattern defines the skeleton of an algorithm and allows
subclasses to provide the implementation for one or more steps.
# Need for Template Method Pattern
Assume you are running a cafe that offers both coffee and tea drinks.
Tea and coffee is prepared in a similar manner:
![[Screenshot 2025-12-25 at 8.12.50 PM.png]]
It is apparent that code is being duplicated.  If a new beverage is added, say `Latte` then, code will be duplicated again.
Furthermore, deciding which beverage to create an object for is full of conditionals. 
If the base recipe changes, you will have to modify all the subclasses which violates [[SOLID#**O**pen for extension, closed for modification|Open/closed principle]].
````tabs
tab: Coffee.java
```java ln:true
public class Coffee {
	void prepare() {
		boilWater();
		brewCoffeeGrinds();
		pourInCup();
		addSugarAndMilk();
	}
	
	public void boilWater() {
		System.out.println("Boiling water");
	}
	
	public void brewCoffeeGrinds() {
		System.out.println("Dripping coffee through filter");
	}
	
	public void pourInCup() {
		System.out.println("Pouring into cup");
	}
	
	public void addSugarAndMilk() {
		System.out.println("Adding sugar and milk");
	}
}
```
tab: Tea.java
```java ln:true
public class Tea {
	void prepare() {
		boilWater();
		steepTea();
		pourInCup();
		addLemon();
	}
	
	public void boilWater() {
		System.out.println("Boiling water");
	}
	
	public void steepTea() {
		System.out.println("Steeping the tea");
	}
	
	public void pourInCup() {
		System.out.println("Pouring into cup");
	}
	
	public void addLemon() {
		System.out.println("Adding lemon");
	}
}
```
````
The solution to this, is to abstract out the common parts of the recipe as seen:
![[Screenshot 2025-12-25 at 8.28.29 PM.png]]
We can further abstract the recipe by considering 
1. `brewCoffeeGrinds()` and `steepTeaBag()` as `brew()` 
2. `addLemon()` and `addSugarAndMilk` as `addCondiments()` 

```java
public abstract class CaffeineBeverage {
	final void prepare() {
		boilWater();
		brew();
		pourInCup();
		addCondiments();
	}
	
	abstract public void brew();
	abstract public void addCondiments();
	
	public final void boilWater() {
		System.out.println("Boiling water");
	}
	
	public final void pourInCup() {
		System.out.println("Pouring into cup");
	}
}

public class Coffee extends CaffeineBeverage {
	public void brew() {
		System.out.println(“Dripping Coffee through filter”);
	}
	
	public void addCondiments() {
		System.out.println(“Adding Sugar and Milk”);
	}
}

public class Tea extends CaffeineBeverage {
	public void brew() {
		System.out.println(“Steeping the tea”);
	}
	
	public void addCondiments() {
		System.out.println(“Adding Lemon”);
	}
}
```
The `prepare()` method is declared as `final` so that subclasses cannot change the recipe. Similarly `boilWater()` and `pourInCup()` are also declared as `final`, so that subclasses cannot change the steps that they are not allowed to.

What if a customer wants black coffee or tea without lemon? Currently, our code forces the addition of condiments. 
Instead we should make the `addCondiments()` method conditionally executable in our template, using a _hook_.

```java hl:6-8,22-24
public abstract class CaffeineBeverage {
	final void prepare() {
		boilWater();
		brew();
		pourInCup();
		if(customerWantsCondiments()) {
			addCondiments();
		}
	}
	
	abstract public void brew();
	abstract public void addCondiments();
	
	public final void boilWater() {
		System.out.println("Boiling water");
	}
	
	public final void pourInCup() {
		System.out.println("Pouring into cup");
	}
	
	public boolean customerWantsCondiments() {
		return true;
	}
}
```

_Hooks_ are optional steps in an algorithm. Notice `customerWantsCondiments()` is ___NOT___ declared `final`. i.e. we allow subclasses to optionally, override the method to provide their own implementation.

# UML Diagram
![[Screenshot 2025-12-25 at 8.39.06 PM.png]]