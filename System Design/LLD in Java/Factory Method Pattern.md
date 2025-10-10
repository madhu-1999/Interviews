#lld #system-design #design-patterns #java

# Definition
Define an interface for creating an object, but let subclasses decide which class to instantiate.
# 

>[!note]
>Here _interface_ refers to a concrete class implementing from a _supertype_ which can be class or interface

^ece0a6
# Need for Factory Pattern
Every time we use **new** operator to instantiate an object like so:
```java ln:false
Pizza pizza = new VeggiePizza()
```
where `Pizza` is an interface and `VeggiePizza` its concrete implementation, we are making a _high level module_ (`Pizza`) depend on a _low-level module_ (`VeggiePizza`) 
This is a violation of  [[SOLID#**D**ependency Injection|Dependency Inversion Principle]].

When you have an _interface_ (`Pizza`) that is implemented by a lot of _concrete classes_, then inevitably we would have instantiation code that looks like this:

```java hl:4-10
Pizza orderPizza (String type) {
	Pizza pizza;
	
	if (type.equals(“cheese”)) { 
		pizza = new CheesePizza();
	} else if (type.equals(“greek”) { 
		pizza = new GreekPizza();
	} else if (type.equals(“pepperoni”) { 
		pizza = new PepperoniPizza();
	}
	
	pizza.prepare(); 
	pizza.bake(); 
	pizza.cut(); 
	pizza.box(); 
	
	return pizza;
}
```

And every time a new _concrete class_ is added, if-else statements will have to be modified.
This is a very error-prone and violates [[SOLID#**O**pen for extension, closed for modification|Open/closed principle]].

# Simple Factory
In accordance with the design principle: ___Identify parts of your application that changes, and encapsulate it so that it is easier to alter/extend without affecting other parts___, we find that the highlighted portion of the code is the only part that __changes__.  So we encapsulate it into an object whose sole purpose is to create _pizzas_.

```java
public class SimpleFactory {
	public Pizza createPizza(String type) {
		Pizza pizza = null;
		
		if (type.equals("cheese")) { 
			pizza = new CheesePizza();
		} else if (type.equals("greek") { 
			pizza = new GreekPizza();
		} else if (type.equals("pepperoni") { 
			pizza = new PepperoniPizza();
		}
		
		return pizza;
	}
}
```

^9ac87b

This still violates __Open /closed__ principle, but now we only have to make changes in one place, instead of multiple places in the code base that needs to instantiate a `Pizza` object.

It violates **Dependency Inversion** principle since `PizzaStore` (a high level abstraction) depends on `SimpleFactory` (a low-level abstraction). Any change in `SimplePizzaFactory` risks breaking `PizzaStore`. 

```java hl:10,2-6
public class PizzaStore {
	SimplePizzaFactory factory; // composition
	
	public PizzaStore(SimplePizzaFactory factory) {
		this.factory = factory;
	}
	
	Pizza orderPizza (String type) {
		Pizza pizza;
		pizza = factory.createPizza(type);
		pizza.prepare(); 
		pizza.bake(); 
		pizza.cut(); 
		pizza.box(); 
	
		return pizza;
	}
	// other methods
}
```

>[!note]
>Simple Factory is ***NOT*** a design pattern. It is the factory pattern in its simplest form.

# Factory Method Pattern
What if we need to create another more than one type of factory i.e support different types of object instantiation?
This is where the Factory Method Pattern comes into play.

## Creating multiple factories
Suppose we are opening franchise stores of our `PizzaStore` in NY and Chicago which will respectively serve NY style pizzas (thin crust, less cheese) and Chicago style pizzas (thick crust, lots of cheese). 
This means we will need 2 different factories, one for NY style pizzas and another for Chicago style pizzas.
All franchises also need to follow some common rules for say baking, cutting and boxing pizzas.

To accommodate these changes, we make `Pizzastore` an [[Abstract classes|abstract class]] with two methods: `orderPizza()` which provides common functionality for creating, baking, cutting and boxing pizzas to franchise stores, and a abstract method `createPizza()` which will be overriden by subclasses (`NYStylePizzaStore` and `ChicagoStylePizzaStore`) to create pizzas (factory method).

Notice this satisfies the [[#Definition]] of factory method pattern. We have an [[#^ece0a6|interface]] (`PizzaStore`) which is deferring the instantiation of objects to its subclasses (`NYStylePizzaStore` and `ChicagoStylePizzaStore`).

This **decouples** the code since `orderPizza()` method does not know which type of pizza (NYStyle or ChicagoStyle) it is preparing, it just know it is preparing a `Pizza`. i.e. `PizzaStore` (a high-level abstraction) depends on `Pizza` (a high-level abstraction). 
Unlike [[#Simple Factory]] , `PizzaStore` is not dependent on its subclasses 
Thus it is not violating **Dependency Inversion Principle** anymore.

If we want to add some new pizzas, say `CaliforniaStyleVeggiePizza` etc.. we can create a new subclass of `PizzaStore` --- `CaliforniaStylePizzaStore` and override `createPizza()` to create the new pizzas. Thus, it does not violate **Open/closed principle** anymore.

![[Screenshot 2025-09-29 at 12.33.33 PM.png]]
>[!note]
>`PizzaStore` is an abstract class here, but can also be an interface, starting Java 8 ( `orderPizza()` can be a default method)

```java hl:12,4
public abstract class PizzaStore {
	public Pizza orderPizza(String type) {
		Pizza pizza;
		pizza = createPizza(type);
		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		return pizza;
	}
	
	protected abstract Pizza createPizza(String type);
	//other methods 
}
```
## Working of a factory method
It is same as [[#^9ac87b|SimplePizzaFactory]]. The if/else code is encapsulated by the `createPizza()` method of the subclasses. For ex:

```java
public class NYStylePizzaStore extends PizzaStore {
	public Pizza createPizza(String type) {
		Pizza pizza = null;
 		if (type.equals("cheese")) { 
			pizza = new NYStyleCheesePizza();
		} else if (type.equals("clam") { 
			pizza = new NYStyleClamPizza();
		} else if (type.equals("pepperoni") { 
			pizza = new NYStylePepperoniPizza();
		} else if (type.equals("veggie")) {
			pizza = new NYStyleVeggiePizza();
		}
		return pizza;
	}
}
```

Notice that we return an instance of `Pizza` class .i.e. all the different kinds of pizzas implement the `Pizza` [[#^ece0a6|interface]].
![[Screenshot 2025-09-29 at 1.00.44 PM.png]]
# UML Class Diagram
![[Screenshot 2025-02-21 at 1.09.34 PM.png]]
`Product` => `Pizza`

`ConcreteProduct` => `NYStyleCheesePizza`, `ChicagoStyleClamPizza` etc..

`Creator` => `PizzaStore`

`ConcreteCreator` => `NYStylePizzaStore`, `ChicagoStylePizzaStore`

# Disadvantages
+ The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern. 
# References
+ Head First Design Patterns
+ https://refactoring.guru/design-patterns/factory-method