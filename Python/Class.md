#python 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
``````table-of-contents
title: 
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
+ Since everything in Python is an object, **class is also an object**.
+ Programmer defined type:
```python
class Time:
    """Represents a time of day."""
    

lunch = Time() # object
type(lunch) # __main__.Time since class Time is defined in main module

# Attributes can be assigned to object directly
lunch.hour = 1
lunch.minute = 30
lunch.second = 0

print(f"{lunch.hour:02d}:{lunch.minute:02d}:{lunch.second:02d}")
# 01:30:00
```
+ Objects are **mutable**. Refer [[Functions#Pass by Reference or Pass by Value?|for more info]]
+ `==` operator checks **identity** for objects, same as [[Conditionals#Is|is]] operator.
+ `isinstance(lunch, Time)` to check if object is instance of a class
+ `hasattr(lunch, 'hour')` to check if an object has a attribute
+ `vars(lunch)` to get all attributes and values of an object in a dict
# Constructor
+ `__init__` method defines constructor of class.
+ Constructor overloading done using [[Functions#Default Arguments|default arguments]] and [[Functions#Variable length arguments|variable length arguments]]
```python
class Person:
    def __init__(self, name, age=None):
        self.name = name
        self.age = age


# Example Usage
person1 = Person('Alice')
person2 = Person('Bob', 25)

# Output
print(person1.name, person1.age)
print(person2.name, person2.age)
```
# Variables
+ `self` refers to current context (think `this` in Java). The name `self` is a convention, ***NOT*** a keyword.
+ `self.name` & `self.age` are **instance variables** i.e. each object has own copy.
+ `species` is a **class variable** i.e. all objects share same copy.
```python
class Person:
	species = 'Homo Sapiens'
    def __init__(self, name, age=None):
        self.name = name
        self.age = age
    
```
## Dynamic Class and Instance Variables
+ Python allows addition of new class and instance variables dynamically, using `setattr(object, attribute, value)`.
```python
 class Record:
     """Hold a record of data."""

john_record = Record();
john = {
	'name': 'John Doe',
	'age': '23',
	'department': 'Engineering'
}

for field, value in john.items():
	setattr(john_record, field, value)

print(john_record.name) # John Doe
print(john_record.age) # age
``` 
# Methods
| Type         | Decorator       | Parameter          | Instance Access | Class Access | Use Case                                                                                   |
| ------------ | --------------- | ------------------ | --------------- | ------------ | ------------------------------------------------------------------------------------------ |
| **Instance** | None needed     | `self`             | ✅               | ✅            | Operations on individual instances.                                                        |
| **Class**    | `@classmethod`  | `cls`              | ❌               | ✅            | Factory methods, alternative constructors, or any method that deals with class-level data. |
| **Static**   | `@staticmethod` | No `self` or `cls` | ❌               | ❌            | Utility methods that don’t need instance or class data.                                    |
```python
class Pizza:
    def __init__(self, toppings):
        self.toppings = list(toppings)
    
    def __repr__(self):
        return f'Pizza[{self.toppings}]'
        
    def add_toppings(self, topping):
        self.toppings.append(topping)
        return 
        
    @classmethod
    def margherita(cls):
        return cls(['mozarella', 'tomatoes'])
        
    @staticmethod
    def get_size_in_inches(size):
        """Returns the diameter in inches for common pizza sizes."""
        size_map = {
            "small": 8,
            "medium": 12,
            "large": 16,
        }
        return size_map.get(size, "Unknown size")

# Instance method
pizza = Pizza([])
pizza.add_toppings('cheese')
print(pizza) # O/P: Pizza[['cheese']]

#Class method
print(Pizza.margherita()) #O/P Pizza[['mozarella', 'tomatoes']]
print(pizza.margherita()) #O/P Pizza[['mozarella', 'tomatoes']]

#Static method
print(pizza.get_size_in_inches('small')) # 0/P: 8
print(Pizza.get_size_in_inches('small')) # 0/P: 8
```
## Instance Methods
+ Can access instance variables and instance methods using `self`.
+ Can access class itself using `self.__class__` attribute.
+ `add_toppings` is an instance method.
+ Can call using dot notation `Pizza().add_toppings('cheese')`. Python automatically passes in the `self` parameter.
## Class Methods
+ Can access only class level data through `cls` parameter. Note that the name `cls` is a convention ***NOT*** a keyword. `cls` is the class object i.e `<class '__main__.Pizza'>`
+ `@classmethod` [[Decorators|decorator]] is used to differentiate between class method and instance method.
+ Class methods can be used in implementing [[Design Principles Rough notes#Factory Pattern|Factory Pattern]] to create factory methods like `margherita()` that return class instances with specific configurations. i.e `cls()` creates a class instance/object internally using `__init__()` constructor. We can call instance methods and access instance variables through this instance.
+ Class methods can thus be used to create alternative constructors.
+ Method can be accessed using class name or object with dot notation.
## Static Methods
+ `@staticmethod` [[Decorators|decorator]] is used to differentiate between static method and instance method.
+ Cannot access class or instance level data. Used to create utility functions that organizationally are a part of the class.
+ Method can be accessed using class name or object with dot notation.
# Public vs Private Members
+ Python ***DOESN'T*** distinguish between public, private, protected attributes like Java.
+ However, we can use naming conventions to distinguish between them.

|Member|Naming|Examples|
|---|---|---|
|Public|Use the normal naming pattern.|`radius`, `calculate_area()`|
|Non-public|Include a leading underscore in names.|`_radius`, `_calculate_area()`|
+ Public members can be accessed outside the class while non-public members ***SHOULD NOT*** be accessed. Functionally, they can be accessed like any other variable/method `obj._radius` or `obj._calculate_area()`.
## Name Mangling
+ Another naming convention for non-public members is to add 2 leading underscores to attribute and method names.
+ Python automatically prepends the classname to it like `_ClassName_.__attr` or `_ClassName_.__method()`. This results in name hiding and they can no longer be accessed using the object and dot notation
```python
class SampleClass:
	def __init__(self, value):
		self.__value = value

	def __method(self):
		print(self.__value)

sample = SampleClass(23)

print(sample.__value) # AttributeError
sample.__method() # AttributeError

print(_SampleClass_.__value) # 23
_SampleClass_.__method() # 23
```
+ However, they can still be accessed by using their fully qualified name: `_ClassName_.__attr` or `_ClassName_.__method()`.
# Getters and Setters vs Properties
+ Getter and Setter example:
```python
class Person:
	def __init__(self, name):
		self.set_name(name)

	def get_name(self):
		return self._name

	def set_name(self, name):
		self._name = name
```
+ This pattern is not popular in Python. It is normal to expose the attribute itself as part of the public API. If some functional behavior is required on top of the attribute, turning it into a property is prefered.
+ Property example: Here `name` is property for which getter and setter is automatically called and `_name` is the attribute which is being manipulated.
```python
class Person:
	def __init__(self, name):
		self._name = name

	@property
	def name(self):
		return self._name

	@name.setter
	def name(self, value):
		self._name = value.upper()

person = Person('Jane')
print(person.name) # JANE

person.name = 'Jane Doe'
print(person.name) # JANE DOE
```
# \__dict__  and vars() function
+ Every object has a  **__dict__** attribute which is a dictionary that stores all the attributes and methods of that object. Remember, everything is an object in Python.
+ For user defined objects and classes:
	+ Object: \__dict__ attribute returns dictionary of user defined attributes with values only.
	+ Class: \__dict__ attribute returns dictionary of user defined and inbuilt attributes + functions.
```python
class DogClass:
    def __init__(self,name,color):
        self.name = name
        self.color = color
    
    def bark(self):
        if self.color == "black":
            return True
        else:
            return False
dc = DogClass('rudra','white')
print(dc.__dict__)
# Output: {'name': 'rudra', 'color': 'white'}

DogClass.__dict__

"""
Output: mappingproxy({'__module__': '__main__',
              '__init__': <function __main__.DogClass.__init__(self, name, color)>,
              'bark': <function __main__.DogClass.bark(self)>,
              '__dict__': <attribute '__dict__' of 'DogClass' objects>,
              '__weakref__': <attribute '__weakref__' of 'DogClass' objects>,
              '__doc__': None})
"""
```
+ vars(***object***) function  returns \_dict__ attribute of the object that was passed in. If no argument is passed it acts as the locals() function i.e. returns all inbuilt and user-defined attributes in the global scope.
# [[Data Classes]]
