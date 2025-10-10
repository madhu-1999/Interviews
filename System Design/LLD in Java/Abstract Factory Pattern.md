#system-design #lld #design-patterns #java
# Prerequisite
[[Factory Method Pattern]]
# Definition
The Abstract Factory Pattern provides an interface for creating families of related or dependent objects without creating a dependency on their concrete classes.
# Need for Abstract Factory Pattern
Continuing with the `PizzaStore` example, we know each of its subclasses `NYStylePizzaStore` and `ChicagoStylePizzaStore` prepares the same pizza say Clam Pizza_ differently (`NYStyleClamPizza` and `ChicagostyleClamPizza`).

Naturally, both pizzas are prepared using the same basic components: _dough_, _sauce_, _toppings_ and _cheese_ but different ingredients.
![[Pasted image 20250929181241.png]]
We want each store to use its own set of ingredients but we don't want the ingredients to be tightly coupled with the creation of `Pizza`.

In Factory Method Pattern, we created a factory to instantiate different `Pizza` subtypes, however here we have to create multiple _ingredient types_ in a concrete factory : dough, sauce, cheese and toppings.
All these are **related** to each other and each type (Ex: dough) has multiple subtypes (Ex: ThinCrustDough, ThickCrustDough) i.e. we have to create **multiple families** of objects.^[[#Definition]]

This is not possible using the Factory Method Pattern. We have to use Abstract Factory Pattern.
# Abstract Factory
We create an interface `PizzaIngredientFactory`  which contains one create method per ingredient type.

Each of the ingredient types `Dough`, `Sauce`, `Cheese`, `Veggies`, `Pepperoni` and `Clams` are also interfaces, which its subclasses like `ThinCrustDough`, `ThickCrustDough` etc.. will implement.

```java
public interface PizzaIngredientFactory {
	public Dough createDough();
	public Sauce createSauce();  
	public Cheese createCheese(); 
	public Veggies[] createVeggies(); 
	public Pepperoni createPepperoni(); 
	public Clams createClam();
}
```

Its subclasses `NYPizzaIngredientFactory` and `ChicagoPizzaIngredientFactory` will implement these methods to specify the ingredients it needs to create `Pizza`.
>[!note]
>We assume that each _style_ of pizzas i.e. NY style and Chicago style uses a common set of ingredients instead of each `Pizza` subtype using different ingredients.

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
	public Dough createDough() {
		return new ThinCrustDough(); 	
	}
	public Sauce createSauce() {
		return new MarinaraSauce(); 
	}
	public Cheese createCheese() {
		return new ReggianoCheese(); 
	}	
	...	
}
```

Subclasses of the `Pizza` interface use the ingredients supplied by the factory to `prepare()` a pizza.

```java hl:10
public abstract class Pizza { 
	String name;
	Dough dough;  
	Sauce sauce; 
	Veggies veggies[]; 
	Cheese cheese; 
	Pepperoni pepperoni; 
	Clams clam;
	
	abstract void prepare();
	//other methods
}
```

Subclasses of `Pizza` like `NYStyleCheesePizza` and `ChicagoStyleCheesePizza` differ only in the ingredients needed to prepare it. 
So we can now abstract out `CheesePizza` , such that using the ingredients supplied by factory method it can prepare a NY style cheese pizza or a Chicago style cheese pizza. 
`CheesePizza` does not know which ingredient factory is used or what ingredients are supplied, so the code is **loosely coupled**.

```java
public class CheesePizza extends Pizza {
	PizzaIngredientFactory ingredientFactory;
	
	public CheesePizza(PizzaIngredientFactory ingredientFactory) {
		this.ingredientFactory = ingredientFactory;
	}
	void prepare() {
		dough = ingredientFactory.createDough(); 
		sauce = ingredientFactory.createSauce(); 
		cheese = ingredientFactory.createCheese();
		...
	}
}
```

>[!note]
>Since `CheesePizza`  (a low level abstraction) depends on  `PizzaIngredientFactory` (a high level abstraction), this does not violate [[SOLID#**D**ependency Injection|Dependency Inversion Principle]].

We need to pass an instance of `PizzaIngredientFactory` to `CheesePizza` , which means we need to make changes to concrete classes of `PizzaStore`.

```java hl:2-6
public class NYStylePizzaStore extends PizzaStore {
	PizzaIngredientFactory ingredientFactory;
	
	public NYStylePizzaStore(PizzaIngredientFactory ingredientFactory) {
		this.ingredientFactory = ingredientFactory;
	} //[[Core Principles#**Constructor injection**|Constructor Injection]] 
	
	public Pizza createPizza(String type){
		Pizza pizza = null; 
		if (item.equals(“cheese”)) {
			pizza = new CheesePizza(ingredientFactory);
			pizza.setName(“New York Style Cheese Pizza”);
		} else if (item.equals(“veggie”)) {
		...
		}
		...
		return pizza;
	}
	
	public static void main(String args[]) {
		PizzaIngredientFactory nyIngredientFactory = new NYPizzaIngredientFactory();
		//Dependency Injection
		PizzaStore pizzaStore = new NYStylePizzaStore(nyIngredientFactory);
		
		pizzaStore.createPizza("cheese");
	}
}
```

Adding new variants of existing families is easy (ex: `CheeseBurstDough`), just create a new subclass that extends required interface (`Dough`). [[SOLID#**O**pen for extension, closed for modification|Open/closed principle]] is preserved.
# UML Class Diagram
![[Screenshot 2025-09-29 at 7.33.21 PM.png]]
# Reference
Head First Design Patterns