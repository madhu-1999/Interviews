---
tags:
  - python
---
# Overview
+ [*] Everything in Python is an object i.e. it a subclass of the `Object` class.
	+ [!] The only exception is classes that subclass `BaseException` i.e. all [exceptions](Python%20-%20Exceptions.md).
+ [i] The __base class__ will contain common methods and functions. The __derived__ classes can override [methods](Python%20-%20Functions.md) and [attributes](Python%20-%20Classes%20and%20Objects.md) in the __base__ class to provide their own implementation.
+ [i] __Derived__ classes can have other methods that are not in the __base__ class.
	+ [!] These methods __SHOULD'NT__ overload base class methods, as it will ==hide the base class method==.
+ [!]  All methods, even instance methods are attached to the _class_, but instance attributes live in the object so, if a super class is not initialized, we cannot access its attributes, but we can access it's methods.
# Basic Syntax
```python
class BaseClass:  
	pass  
  
class DerivedClass(BaseClass):  
	pass
```
# Method overriding
Method overriding allows, a derived class (or subclass) to **provide a specific implementation of a method** that is already defined in its base class (or superclass).
+ [*] The derived class method must have the same name, and same parameters as the base class.
```python
class DataModel:  
	def evaluate(self, predictions, true_values):  
		print("Evaluating model...")  
		# Implementation of a basic evaluation metric  
  
class ClassificationModel(DataModel):  
	def evaluate(self, predictions, true_values):  
		print("Evaluating classification model...")  
		# Implementation of classification-specific evaluation metrics, e.g., accuracy, F1 score  
  
class RegressionModel(DataModel):  
	def evaluate(self, predictions, true_values):  
		print("Evaluating regression model...")  
		# Implementation of regression-specific evaluation metrics, e.g., MSE, RMSE
```

>[!warning] Since [](Python%20-%20Functions.md#Method%20Overloading|method%20overloading) is supported differently in Python, we cannot overload base class methods.
```run-python
class Base:
    def greet(self):
        print('Hi')
        
class Derived(Base):
    def greet(self, name):
        print(f'Hi {name}')
        
o = Derived()
# This will throw TypeError since Python expects us to pass an argument.
o.greet()
o.greet('Maria')
```
# `super()` method

It returns a **temporary object of the superclass** that allows access to its methods.
+ [p] __Code maintenance__: By not hard-coding the parent class's name, `super()` makes the code more adaptable and easier to update.
## Calling instance methods
>[!important] Calling super class instance methods:
>__Inside instance method of derived class__
>Python automatically binds the parent's method to the current instance (`self`).
>
>__Inside class/static method of derived class / outside derived class__
>Explicitly pass the object instance using `super(DerivedClass, instance)` 

```run-python
class Parent:
    def greet(self):
        return "Hello from Parent"

class Child(Parent):
     # Calling superclass instance method from derived class instance method
    def greet(self):
        # Accessing an instance method
        parent_greeting = super().greet()
        return f"{parent_greeting}, and Welcome from Child"
    
    # Calling superclass instance method from derived class classmethod/staticmethod
    @classmethod
    def classGreeting(cls):
        parent_greeting = super(Child, Child()).greet()
        return f"{parent_greeting}, and Welcome from Child classmethod"
        
o = Child()
# O/P: Hello from Parent, and Welcome from Child
print(o.greet())
# O/P: Hello from Parent, and Welcome from Child classmethod
print(o.classGreeting())
# O/P: Hello from Parent
print(super(Child, o).greet())
```
## Calling class methods
>[!important] Calling super class class methods:
>__Inside class method of derived class__
>Python automatically binds the parent's method to the current class (`cls`).
>
>__Inside instance/static method of derived class / outside derived class__
>Explicitly pass the class context `cls` using `super(DerivedClass, DerivedClass)` 

```run-python
class Parent:
    @classmethod
    def greet(cls):
        return "Hello from Parent"

class Child(Parent):
     # Calling superclass class method from derived class instance/static method
    def greet(self):
        parent_greeting = super(Child, Child).greet()
        return f"{parent_greeting}, and Welcome from Child"
    
    # Calling superclass class method from derived class classmethod
    @classmethod
    def classGreeting(cls):
        parent_greeting = super().greet()
        return f"{parent_greeting}, and Welcome from Child classmethod"
        
o = Child()
# O/P: Hello from Parent, and Welcome from Child
print(o.greet())
# O/P: Hello from Parent, and Welcome from Child classmethod
print(o.classGreeting())
# O/P: Hello from Parent
print(super(Child, Child).greet())
```
## Calling static methods
>[!important] Calling super class class methods:
>__Inside class method of derived class__
>Python automatically binds the parent's method to the current class (`cls`).
>
>__Inside instance/static method of derived class / outside derived class__
>Explicitly pass the class context `cls` using `super(DerivedClass, DerivedClass)` 

```run-python
class Parent:
    @staticmethod
    def greet():
        return "Hello from Parent"

class Child(Parent):
     # Calling superclass staticmethod method from derived class instance method
    def greet_instance(self):
        parent_greeting = super(Child, Child).greet()
        return f"{parent_greeting}, and Welcome from Child"
    
    # Calling superclass static method from derived class classmethod
    @classmethod
    def classGreeting(cls):
        parent_greeting = super().greet()
        return f"{parent_greeting}, and Welcome from Child classmethod"
    
    @staticmethod
    def greet():
         parent_greeting = super(Child, Child).greet()
         return f"{parent_greeting}, and Welcome from Child staticmethod"
        
o = Child()
# O/P: Hello from Parent, and Welcome from Child
print(o.greet_instance())
# O/P: Hello from Parent, and Welcome from Child classmethod
print(o.classGreeting())
# O/P: Hello from Parent, and Welcome from Child staticmethod
print(o.greet())
# O/P: Hello from Parent
print(super(Child, Child).greet())
```

## Using with [constructors](Python%20-%20Constructors.md)
```run-python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Calls the parent class's __init__
        self.breed = breed

o = Dog('Espio', 'Pomsky')
print(o.name) # O/P:Espio
```

>[!warning] `super().__init__()` is called _implicitly_ only if the derived class does not specify its own `.__init__()` function!
>>[!note] The implicit call only applies to constructors with no parameters

```run-python
class Base:
    def __init__(self):
        self.count = 0
        
    def greet(self):
        print('Hi')
        
class Derived(Base):
    # Did not explicitly call super().__init__()
    def __init__(self):
        self.name = 'Maria'
        
    def bye(self):
        print('Bye')
        
o = Derived()
# Didn't call super().__init_(), but can access!
o.greet() # Hi
# Cannot access super class attributes since it is uninitialized
print(o.count) # This will throw a AttributeError
```

>[!faq] Why can we access super class methods and not attributes?
>All methods, even instance methods are attached to the _class_, but instance attributes live in the object i.e. if a super class is not initialized, we cannot access its attributes, but we can access it's methods.
# Accessing attributes in derived class
## Instance attributes
Instance attributes belong to a specific object. For a derived class to inherit them, it **must** call the parent class's `__init__` constructor, ideally using `super()`.

```run-python
class Parent:
    def __init__(self, name):
        self.name = name  # Instance attribute

class Child(Parent):
    def __init__(self, name, age):
        # Initialize the parent class attributes
        super().__init__(name) 
        self.age = age

    def display(self):
        print(f"Name: {self.name}, Age: {self.age}")
        
o = Child('Tom' , 5)
o.display() # O/P: Name: Tom, Age: 5
```
## Class attributes
Class attributes are shared by all instances of a class. A derived class can access them directly using `self`, the class name, or `super()`.

```run-python
class Parent:
    species = "Human"  # Class attribute

class Child(Parent):
    def show_species(self):
        print(self.species)       
        print(Parent.species)        
        # Note: super().species also works 

o = Child()
o.show_species()
# O/P: Human\n Human
```
## Public/protected/private attributes
```run-python
class Parent:
    def __init__(self):
        self.public_attr = "I am public"
        self._protected_attr = "I am protected"
        self.__private_attr = "I am private"

class Child(Parent):
    def access_parent_attributes(self):
        # 1. Public
        print(self.public_attr) 
        
        # 2. Protected
        print(self._protected_attr) 
        
        # 3. Private: This will raise an AttributeError!
        # print(self.__private_attr) 
        
        # How to actually access it (Name Mangling workaround):
        # Python rewrites __private_attr as _ClassName__private_attr
        print(self._Parent__private_attr) 

# Verification
obj = Child()
obj.access_parent_attributes()
# O/P:
# I am public 
# I am protected 
# I am private
```
# Multiple Inheritance
Python supports multiple inheritance i.e. a class can inherit from multiple classes.
```run-python
class Father:  
	def gardening(self):  
		print("I enjoy gardening")  
  
class Mother:  
	def cooking(self):  
		print("I love cooking")  
  
class Child(Father, Mother): # Inherits from both Father and Mother  
	def sports(self):  
		print("I enjoy sports")  
  
# Using the Child class  
child = Child()  
child.gardening() # Inherited from Father  
child.cooking() # Inherited from Mother  
child.sports() # Child's own method
```
## Diamond Problem & Resolution
Atleast two super classes of a class, that inherits from multiple classes, in turn inherit from a common ancestor
![Python - Inheritance-1781748535562](Assets/Python%20-%20Inheritance-1781748535562.webp)
When `D` calls a method provided by grandparent class `A` using `super()` , how does Python figure out if it should call `B's` implementation or `C's` implementation?
+ [I] The answer is __Method Order Resolution__. 
```run-python
class A:
    def show(self):
        print("Process from A")

class B(A):
    def show(self):
        print("Process from B")

class C(A):
    def show(self):
        print("Process from C")

# D inherits from both B and C
class D(B, C):
    pass

obj = D()
obj.show()  # Output: Process from B
print(D.__mro__)
# O/P: (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
```

>Python uses __C3 Linearization Algorithm__ to implement Method order resolution.
	1. First we check if the class has the required method/attribute
	2. If not, we check the parent class inherited first (Here B)
	3. If it doesn't have the method/attribute, we check the next inherited class and so on... (Here A)
	4. Next, it checks, the super classes of the parent classes in order (Here A)
	5. If all else fails, it will check the object superclass.
	6. Throws a `AttributeError`

The `.__mro__` class attribute can be used to understand the method order resolution chain for a class.
# Abstract Base classes
See [Abstract Base Class](Python%20-%20Abstract%20Base%20Class.md) for more information.
# References:
[super() in Python](https://codefinity.com/blog/Using-super-in-Python-and-Its-Pitfalls)
[Python inheritance](https://medium.com/data-bistrot/inheritance-in-python-object-oriented-programming-63bd93d7490c)
[Diamond problem](https://medium.com/@ksandeeptech07/diamond-problem-in-oop-explained-672d136912c8)
[Method Resolution Order](https://medium.com/@ruitcatarino/understanding-pythons-method-resolution-order-mro-f7cbcec36993)