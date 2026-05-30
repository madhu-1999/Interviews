#design-patterns #lld #java #system-design 
# Definition
The Decorator pattern attaches additional responsibilities to an object dynamically. It provides an alternative way to **extend classes without subclassing**.
# Need for Decorator Pattern
Imagine you are running a coffee shop. To represent all the different coffees in your menu, you have a `Beverage` [[Abstract classes|abstract class]] which provides common methods like `cost()` to all subclasses like `Espresso` or `Decaf` .![[Screenshot 2025-10-13 at 11.09.17 AM.png]]
This works well, but what happens when you start offering customers choices for toppings or ingredients like adding milk or sugar, and toppings like whipped cream or caramel on any coffee?
As per the current design, you could end up of hundreds of classes for each combination like `EspressoWithOatMilkAndWhippedCream` and `EspressoWithMilkAndWhippedCream`, which would worsen if, in the future customer could choose multiple toppings.

One way to fix this issue is reworking the `Beverage` class to include all the condiments, and letting subclasses decide which condiments to use and calculate cost accordingly.
![[Screenshot 2025-10-13 at 11.20.41 AM.png]]
But, there are many disadvantages to using this method. 
Every time a new condiment is introduced or price is changed, the `Beverage` class and all its subclasses will have to be modified to accommodate the change. This violates [[SOLID#**O**pen for extension, closed for modification|Open/closed principle]].
Some condiments may be incompatible with some drinks (cannot add milk to a black coffee), but the subclass `DarkRoast` is still forced to inherit and implement methods for them. This violates [[SOLID#**I**nterface Segregation Principle (ISP)|Interface Segregation principle]].
# Decorator Pattern
The coffee (`DarkRoast` , `Decaf` etc..) is _decorated_ with the condiments (`Whip`, `Caramel` etc..) at runtime to customize orders.
![[image.png]]
The _decorators_ (i.e. condiments) _wrap_ the object (i.e. coffee) , thus their supertypes are the same (`Beverage`). This way, multiple _decorators_ can be wrapped around the object.
Each _decorator_ has its own `cost()` function (and so does the concrete object), which is called in order, starting from the outermost object/decorator to calculate the total cost.
This way, pricing changes for a condiment or a type of coffee are localized to its own class. Below is an example of a concrete class `Espresso`:

```java
public class Espresso extends Beverage {

	public Espresso() {
		description = "Espresso";
	}
	
	public double cost() {
		return 1.99;
	}
}
```

We can create an abstract class `CondimentDecorator` which provides some common functionality for all condiments subclasses like `Milk` and `Whip`. 

```java
public abstract class CondimentDecorator extends Beverage {
	
	public abstract String getDescription();
}
```

Lets see an example of a concrete condiment `Milk`:

```java
public class Milk extends CondimentDecorator {
	/* 
	Object being `decorated`. Can be a concrete component or
	another decorator
	 */
	Beverage beverage;
	
	// [[Core Principles#**Constructor injection**|Constructor injection]] 
	public Milk(Beverage beverage) {
		this.beverage = beverage;
	}
	
	public String getDescription() {
		return beverage.getDescription() + ", Milk";
	}
	
	// Adds it own cost to that of the object being `decorated`.
	public double cost() {
		return .20 + beverage.cost();
	}
}
```

Now, lets see how it all comes together. Here the [[Factory Method Pattern]] is used to centralize object creation.

```java
public class BeverageFactory {
// `baseBeverage` is the object being decorated.
	public static Beverage createBeverage(Beverage baseBeverage, List<String> condiments) {
	
	Beverage beverage = baseBeverage;
		for(String condiment: condiments) {
			switch(condiment.toLowerCase()) {
			// MILK, WHIP are enums
				case MILK:
					beverage = new Milk(beverage);
					break;
				case WHIP:
					beverage = new Whip(beverage);
					break;
				...
			}
		}
		return beverage;
	} 
}
```

Looking at the [[#Definition]], it says the decorator pattern allows us to extend classes without subclassing. However, if we look at all the above code, we see that `CondimentDecorator` is in fact, subclassing the `Beverage` class. How do these two things, reconcile?

`CondimentDecorator` subclasses `Beverage` , but that is done, only to match the supertype. The _behavior_ of adding condiments is not being subclassed from `Beverage` , it is unique to the _decorator_. Thus, we can say that, the decorator allows us to extend functionality of a class without subclassing.

# UML Class Diagram
![[Screenshot 2025-10-13 at 12.22.54 PM.png]]
# Advantages
1. Can extend behavior of a class without subclassing
2. Can add/remove responsibilities at runtime
3. Can combine several behaviors by wrapping an object using multiple decorators.
# Disadvantages
1. Hard to remove a specific decorator from the decorator stack.
2. A decorator has no knowledge of other decorators in the stack, since they are self-contained. Thus, it is hard to write code that depends on a particular order of decorators or the presence/absence of a different decorator.
3. Can end up in the addition of a lot of small classes (harder to track and maintain)