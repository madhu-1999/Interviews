---
tags:
  - python
---
# Overview
An abstract base class is a class that cannot be instantiated directly and often includes one or more abstract methods. These classes serve as blueprints for other classes, enforcing a consistent interface for a group of derived classes.
+ [!] Cannot instantiate object (if atleast one abstract method)
+ [i] 1 or more abstract methods
+ [i] 0 or more non-abstract methods.
+ [i] A subclass must implement all the abstract methods, else it also becomes an abstract class. 
# Creating a abstract base class
```run-python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def sound(self):
        pass
        
Animal() # This will throw a TypeError
```

>[!warning]- If abstract class has no abstract methods, it can be instantiated.
>Refer [](Python%20-%20Duck%20typing.md#^b8a5eb|this) to understand why this happens.

```run-python
from abc import ABC

class Animal(ABC):
    
    def sound(self):
        pass
    
Animal() # This will work!
```
# Subclassing abstract class
```run-python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
    
    @abstractmethod
    def perimeter(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14159 * self.radius * self.radius
    
    def perimeter(self):
        return 2 * 3.14159 * self.radius

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height
    
    def perimeter(self):
        return 2 * (self.width + self.height)

if __name__ == "__main__":
    circle = Circle(5)
    rectangle = Rectangle(4, 6)

    print(f"Circle Area: {circle.area()}, Perimeter: {circle.perimeter()}")
    # Output: Circle Area: 78.53975, Perimeter: 31.4159
    print(f"Rectangle Area: {rectangle.area()}, Perimeter: {rectangle.perimeter()}")
    # Output: Rectangle Area: 24, Perimeter: 20
```

If a subclass does not implement all the abstract methods, it cannot be instantiated as it also becomes a abstract class. A `TypeError` will be thrown, if you try to instantiate it.
```run-python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
    
    @abstractmethod
    def perimeter(self):
        pass

# `perimeter` method not implemented
class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14159 * self.radius * self.radius
    
if __name__ == "__main__":
    circle = Circle(5) # O/P: TypeError
```
# Abstract Properties
In Python, [properties](Python%20-%20Property%20Attributes.md) behave like attributes and methods. We can have abstract properties in a class if the "getter" function is annotated with both `@abstractmethod` and `@property`.

```python
from abc import ABC, abstractmethod
class Animal(ABC):
    @property
    @abstractmethod
    def species(self):
        pass 

class Dog(Animal):
    @property
    def species(self):
        return "Canine"

# Instantiate the concrete subclass
dog = Dog()
print(dog.species)
```
	
# Virtual Subclasses
Python relies on duck typing to allow independent classes to appear as though they are the same type as long as they all contain a given set of method signatures. 
This provides type flexibility and avoids rigid inheritance hierarchies, but at the same time makes it harder to understand what methods constitute a given interface. 
Virtual subclasses provide a middle ground between inheritance hierarchies (via Abstract classes) and type flexibility (via duck typing). [](Python%20-%20Duck%20typing.md#^906522|Refer%20to%20this%20for%20a%20more%20detailed%20explanation)

```run-python
import abc

class Car(abc.ABC):
    @abc.abstractmethod
    def drive(self): ...

    def info(self):
        print('4 wheel vehicle')

class Benz(Car):
    def drive(self):
        print("Driving in Benz")

# This is a independent class
class Tesla:
    def drive(self):
        print("Driving in Tesla")
 
# ===== Main ===== 
# This method expects an object of type `Car`      
def autonomous_driving(car: Car):
    if isinstance(car, Car):
        car.drive()
    else:
        raise Exception("Car not supported")
        
autonomous_driving(Benz()) # O/P: Driving in Benz
autonomous_driving(Tesla()) # O/P: Exception: Car not supported
```

Consider a scenario where our code has a `Car` abstract class that `Benz` subclasses.
Assume, we have a `Tesla` class which comes from a third party library i.e. we cannot change it. Since `Tesla` is an independent class and not a subclass of  `Car`, it cannot be used with the `autonomous_driving()` function.

We can resolve this situation, by making `Tesla` virtually subclass `Car` i.e. ==make it appear as though `Tesla` is a subclass of `Car` , even though it is not==. This is done using the `register()` method.

```run-python
import abc

class Car(abc.ABC):
    @abc.abstractmethod
    def drive(self): ...

    def info(self):
        print('4 wheel vehicle')

class Benz(Car):
    def drive(self):
        print("Driving in Benz")

# This is a independent class
class Tesla:
    def drive(self):
        print("Driving in Tesla")

# Make `Tesla` a virtual subclass of `Car`. 
Car.register(Tesla)

# ===== Main ===== 
# This method expects an object of type `Car`      
def autonomous_driving(car: Car):
    if isinstance(car, Car):
        car.drive()
    else:
        raise Exception("Car not supported")
        
autonomous_driving(Benz()) # O/P: Driving in Benz
autonomous_driving(Tesla()) # O/P: Driving in Tesla
```

This only makes it appear as though `Tesla` is a subclass of `Car`. ==It does not inherit any methods or attributes from `Car`==.

>[!info] A class must either subclass `ABC` or its metaclass must be `ABCMeta` for it to have access to the `register()` method.

# References:
[Abstract classes in Python](https://www.geeksforgeeks.org/python/abstract-classes-in-python/)
[Virtual Subclasses in Python](http://kavianam.ir/Virtual-Subclass-in-Python)