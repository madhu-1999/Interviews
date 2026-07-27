---
tags:
  - "#python"
---
# What is Duck typing?
Since Python is a dynamically typed language i.e. the type of an object can change at runtime and it has no type checking, it does not make sense to rely on the type of the object to determine if we are [[SOLID#**D**ependency Injection|programming to the correct interface]] .
Instead, Python checks if a object has the required method signature. If it does, the operation is valid, else it throws an `AttributeError` ^b8a5eb

>[!tldr] Duck typing allows you to treat objects of different types as the same general type.

Lets understand this, in terms of the [[Strategy Pattern]]. In Java, you must explicitly define an interface. The client code will strictly demand an object that _is-a_ type of that interface.

```run-java

class Main {
	public static void main(String[] args) {
		ShoppingCart cart = new ShoppingCart();
		cart.checkout(100, new CreditCardStrategy());
		cart.checkout(200, new PayPalStrategy());
	}
}

// 1. Define the explicit Strategy Interface
interface PaymentStrategy {
    void pay(int amount);
}

// 2. Concrete Strategy A
class CreditCardStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card.");
    }
}

// 3. Concrete Strategy B
class PayPalStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal.");
    }
}

// 4. The Context Class (Client)
class ShoppingCart {
    // The context explicitly demands a 'PaymentStrategy' type
    public void checkout(int amount, PaymentStrategy paymentMethod) {
        paymentMethod.pay(amount);
    }
}
```

Here `PaymentStrategy` is the interface, and `checkout()` expects a object of type `PaymentStrategy`. If I want to add a new payment option: `UPIPaymentStrategy`, it must implement the `PaymentStrategy` interface.

Python, in contrast, looks at only the method signatures, which means we don't have to worry about inheritance hierarchies, when creating new classes. The individual classes just need to implement the method with required method signature.
```run-python
# 1. Concrete Strategy A (No common base class or formal interface)
class CreditCardStrategy:
    def pay(self, amount: int):
        print(f"Paid {amount} using Credit Card.")

# 2. Concrete Strategy B
class PayPalStrategy:
    def pay(self, amount: int):
        print(f"Paid {amount} using PayPal.")

class UPIStrategy:
    def pay(self, amount:int, id: int = 101):
        print(f"Paid {amount} to {id} using UPI")

# 3. The Context Class (Client)
class ShoppingCart:
    def checkout(self, amount: int, payment_method):
        # Python doesn't check the type of payment_method. 
        # It just blindly calls .pay(). If the object has it, it works!
        payment_method.pay(amount)

# --- Execution ---
cart = ShoppingCart()

# O/P: Paid 100 using Credit Card.
cart.checkout(100, CreditCardStrategy()) 
# O/P: Paid 200 using Paypal.
cart.checkout(200, PayPalStrategy())   
# O/P: Paid 200 to 101 using UPI.
cart.checkout(200, UPIStrategy()) 
```

Because Python only cares about the _behavior_ (`.pay()`), we can satisfy the strategy pattern using any object that is executable in the same way—even a simple function!
```run-python
# 1. Concrete Strategy A (No common base class or formal interface)
class CreditCardStrategy:
    def pay(self, amount: int):
        print(f"Paid {amount} using Credit Card.")

# 2. Concrete Strategy B
class PayPalStrategy:
    def pay(self, amount: int):
        print(f"Paid {amount} using PayPal.")

# A simple function can act as a strategy!
def pay_with_crypto(amount):
    print(f"Paid {amount} using Crypto.")


# 3. The Context Class (Client)
class ShoppingCart:
    def checkout(self, amount: int, payment_method):
        # Python doesn't check the type of payment_method. 
        # It just blindly calls .pay(). If the object has it, it works!
        payment_method(amount)

# --- Execution ---
cart = ShoppingCart()
# O/P; Paid 100 using Credit Card.
cart.checkout(100, CreditCardStrategy().pay) 
# O/P; Paid 200 using PayPal.
cart.checkout(200, PayPalStrategy().pay)   # Works!
# This works because functions can be passed around and behave correctly
# O/P; Paid 300 using Crypto.
cart.checkout(300, pay_with_crypto)
```
# Advantages
+ [p] **High Flexibility and Decoupling:** You can use different objects interchangeably based entirely on their behavior without worrying about rigid type hierarchies. This promotes modularity, making it incredibly easy to swap out components or plug in new ones.
+ [p] **Simpler, Cleaner Code:** It eliminates the need for extensive boilerplate code, such as defining formal interface blocks or managing complex abstract class, inheritance structures. You focus strictly on the required behavior.
+ [p] **Enhanced Code Reuse:** Because classes do not need to inherit from a common superclass, you can easily reuse independent classes across different parts of an application
# Disadvantages
+ [c] **Risk of Runtime Errors:** Because types are not checked at compile time, errors related to missing methods or attributes will only surface when the code is executed. If an unexpected object is passed in and lacks a required method, your program will crash at runtime with an `AttributeError`.

> [!info]- Explanation
> 
> ```run-python
> class Duck:
>     def swim(self):
>         print("The duck is swimming")
> 
>     def fly(self):
>         print("The duck is flying")
> 
> class Swan:
>     def swim(self):
>         print("The swan is swimming")
> 
>     def fly(self):
>         print("The swan is flying")
> 
> class Penguin:
>     def swim(self):
>         print("The penguin is swimming")
>         
> birds = [Duck(), Swan(), Penguin()]
> for bird in birds:
>     bird.swim()
>     bird.fly()
>     
> ''' O/P:
> The duck is swimming
> The duck is flying
> The swan is swimming
> The swan is flying
> The penguin is swimming
> ERROR!
> Traceback (most recent call last):
>   File "<main.py>", line 22, in <module>
> AttributeError: 'Penguin' object has no attribute 'fly'
> '''
> ```
> 
> Here, `Penguin` only implements the `swim()` method, thus, as a whole, these classes partially support duck typing. This would work, without issues, if only `swim()` method was used.
> 
> >[!faq] How to overcome this?
> 
> 1. If the classes `Duck` and `Swan` were implementing a common interface through [[Python - Abstract Base Class|abstract base class]] , we can: 
> 	1. do a `isinstance` check to confirm they are of the expected type.
> 	2. Use static type checking (type hints) to identify incorrect behavior early.
> 2. For this example, where `Duck` and `Swan` are independent classes, to confirm that an object is the expected general type we can check if the object has the required attributes/methods before proceeding.
> ```python
> for bird in birds:
>     if hasattr(bird, "fly"):
>          bird.fly()
> ```

+ [c] **Lack of Explicitness:** Without explicit interface declarations or type signatures, it can be harder for other developers (or your future self) to understand exactly what methods or attributes an object is expected to have just by looking at the code.

> [!info]- Explanation
> 
> ```python
> class Car:
>     def __init__(self, make, model, color):
>         self.make = make
>         self.model = model
>         self.color = color
> 
>     def start(self):
>         print("The car is starting")
> 
>     def stop(self):
>         print("The car is stopping")
> 
>     def drive(self):
>         print("The car is driving")
> 
> class Truck:
>     def __init__(self, make, model, color):
>         self.make = make
>         self.model = model
>         self.color = color
> 
>     def start(self):
>         print("The truck is starting")
> 
>     def stop(self):
>         print("The truck is stopping")
> 
>     def drive(self):
>         print("The truck is driving")
> ```
> 
> # Method 1: Using Abstract Base Classes
> `Car` and `Truck` are independent classes, but they share a common interface i.e. duck typing compliant. Now, imagine these were more complex classes and scattered across the codebase. If we want to create a new class `Jeep` with the same interface, it would involve either:
> 1. Trying to find these classes (`Car` and `Truck`) and understand what methods and attributes are common between them 
> 2. Looking up documentation to understand what the interface is. This requires strong documentation.
> We can eliminate this busy work, by specifying a common interface via an abstract base class, that these classes can implement.
> 
> ```python
> from abc import ABC, abstractmethod
> 
> class Vehicle(ABC):
>     def __init__(self, make, model, color):
>         self.make = make
>         self.model = model
>         self.color = color
> 
>     @abstractmethod
>     def start(self):
>         raise NotImplementedError("start() must be implemented")
> 
>     @abstractmethod
>     def stop(self):
>         raise NotImplementedError("stop() must be implemented")
> 
>     @abstractmethod
>     def drive(self):
>         raise NotImplementedError("drive() must be implemented")
> ```
> 
> This would mean loosing the decoupling provided by duck typing that lets us avoid rigid type hierarchies. However, Python provides a workaround, to use abstract base classes but avoid type hierarchies via [[Python - Abstract Base Class#Virtual Subclasses|virtual subclasses]].
> # Using Protocols (Recommended)
> ```run-python
> from typing import Protocol
> 
> class Vehicle(Protocol):
>     def __init__(self, make, model, color, mileage):
>         self.make = make
>         self.model = model
>         self.color = color
>         self.mileage = mileage
>         
>     def start(self):
>         pass
>     
>     def stop(self):
>         pass
>     
>     def drive(self):
>         pass
>         
> # A pure duck-typed class.
> class Car:
>     def __init__(self, make, model, color):
>         self.make = make
>         self.model = model
>         self.color = color
>         
>     def start(self):
>         print("The car is starting")
> 
>     def stop(self):
>         print("The car is stopping")
> 
>     def drive(self):
>         print("The car is driving")
>         
> # ===== Context ======
> def simulate(vehicle: Vehicle):
>     vehicle.start()
>     vehicle.stop()
>     vehicle.drive()
> 
> def show_specs(vehicle: Vehicle):
>     print(f"Make: {vehicle.make} Model: {vehicle.model} Color: {vehicle.color} Mileage: {vehicle.mileage}")
> 
> car = Car('Toyota', 'Corolla', 'Silver')
> 
> ''' O/P:
> The car is starting
> The car is stopping
> The car is driving
> '''
> simulate(car)
> # This will throw a AttributeError as Car does not have mileage attribute
> show_specs(car)
> ```
> 
> By subclassing `typing.Protocol`, we can establish formal interfaces without reintroducing rigid class hierarchies. This approach preserves the decoupled nature of pure duck typing while offering two key advantages:
> 1. **Better Maintainability:** It centralizes the expected behavior into a single, documented contract, making it much easier for developers to reference and maintain the interface.
> 2. **Early Bug Detection:** Because the protocol acts as a type hint, static type checkers can catch structural mismatches and type bugs before runtime.

^906522

+ [c] **Maintenance and Refactoring Challenges:** It can be difficult to track down every single object that must exhibit a certain behavior. If you decide to rename or alter a method inside a specific class, you might accidentally break a duck-typed interaction somewhere else in the codebase without warning.
# Polymorphism with Duck Typing
It is implemented ==through behavior rather than explicit inheritance==.
Different, completely unrelated classes can be used in the exact same way as long as they share the necessary method signatures.
```run-python
class Duck:
    def speak(self):
        print("Quack!")

class Person:
    def speak(self):
        print("Hello!")

class Robot:
    def speak(self):
        print("Beep boop!")

# A polymorphic function that doesn't care about the object's type
def make_it_speak(entity):
    entity.speak()

# Usage
for entity in [Duck(), Person(), Robot()]:
    make_it_speak(entity)

```

# References:
[Duck Typing in Python](https://realpython.com/duck-typing-python/#getting-to-know-duck-typing-in-python)