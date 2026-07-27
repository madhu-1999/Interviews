---
tags:
  - "#python"
  - "#oop"
---
# Defining a class
```python
import math

class Circle:
	# Constructor
	# `self` is analogous to `this` in Java
    def __init__(self, radius):
        self.radius = radius

    def calculate_area(self):
        return math.pi * self.radius ** 2
```
# Creating an object
```python
>>> from circle import Circle

>>> circle_1 = Circle(42)
>>> circle_2 = Circle(7)

>>> circle_1
<__main__.Circle object at 0x102b835d0>
>>> circle_2
<__main__.Circle object at 0x1035e3910>
```
# Accessing attributes and methods
Use the dot notation to access attributes and methods of an object/class.
```python
>>> circle_1.radius = 100
>>> circle_1.radius
100
>>> circle_1.calculate_area()
31415.926535897932
```

# ☱Instance Attributes
Instance attributes are variables bound to a specific object, meaning their values are entirely unique to that individual instance.
## ☱Accessing instance attributes
>[!warning] Instance attributes must be accessed using:
>+ `self` __WITHIN__ the class
>+ object name __OUTSIDE__ the class

```run-python
class Car:
    def __init__(self, make, model, year, color):
        self.make = make
        self.model = model
        self.year = year
        self.color = color
        self.started = False
        self.speed = 0
        self.max_speed = 200

car = Car(make='Honda', model='Civic', year=2019, color='Blue')
print(f"car object -- model: {car.model}")
another_car = Car(make='Toyota', model='Corolla', year=2023, color='Grey')
print(f"another_car object -- model: {another_car.model}")

```
# ☱Instance methods
An **instance method** is a function defined inside a class that operates on a specific object (instance) of that class.

The defining characteristic of an instance method is that it **must accept `self` as its very first parameter**, which is a reference to the specific object that is currently calling the method.
+ [*] `self` is __NOT__ a keyword, it is a naming convention.
## ☱Accessing instance methods
>[!warning] Instance methods must be accessed using:
>+ `self` __WITHIN__ the class
>+ object name __OUTSIDE__ the class

```run-python
class Car:
    def __init__(self, make, model):
        self.make = make      # Instance attribute
        self.model = model    # Instance attribute
        self.speed = 0        # Instance attribute

    # This is an instance method
    def accelerate(self, increment):
        self.speed += increment
        print(f"The {self.make} is now going {self.speed} km/h.")
car = Car(make='Honda', model='Civic')
car.accelerate(50)
another_car = Car(make='Toyota', model='Corolla')
another_car.accelerate(30)
```

# ☱Class Attributes
+ [i] Analogous to [[Static#Static Variables|Static variables]] in Java  
 These attributes are tied to the class itself rather than to particular objects of that class. i.e. a class attribute and its value is shared between all objects of that class.
 ```python
 class ObjectCounter:
	 num_instances = 0
	 def __init__(self) -> None:
		 ObjectCounter.num_instances += 1
		
obj1 = ObjectCounter()
print(f"Count of instances: {ObjectCounter.num_instances}") # 1
obj2 = ObjectCounter()
print(f"Count of instances: {ObjectCounter.num_instances}") # 2
 ```
## ☱Accessing class attributes
>[!warning] Class attributes can be accessed using:
>+ Classname and object __OUTSIDE__ the class
>+ Classname __WITHIN__ the class (if `self` is used to access a class attribute, it will instead create an instance attribute with same name.)

```run-python
class ObjectCounter:
    # class attribute
    num_instances = 0
    def __init__(self) -> None:
	    # This works because `type(self)` is ObjectCounter i.e. classname
        type(self).num_instances += 1

obj1 = ObjectCounter()
print(f"Count of instances : {obj1.num_instances}")	
obj2 = ObjectCounter()
print(f"Count of instances : {ObjectCounter.num_instances}")	
```

>[!tip] Use `type(self).num_instances` to refer to class attributes __WITHIN__ the class. It is the __recommended__ approach, since we can avoid hardcoding classname.
# ☱Class methods (`@classmethod`)
A **class method** is a method that is bound to the class itself rather than an individual object instance.
The defining characteristic of a class method is that it **must accept `cls` as its very first parameter**, which represents the class object itself.
+ [*] `cls` is __NOT__ a keyword, it is a naming convention.
## ☱Accessing class methods
>[!warning] Class methods can be accessed using:
>+ Classname and object __OUTSIDE__ the class
>+ Classname and `self` __WITHIN__ the class.

```run-python
class Student:
    # A class attribute
    school_name = "Global Academy"

    def __init__(self, name):
        self.name = name

    # This is a class method
    @classmethod
    def get_school_name(cls):
        # Through 'cls', we can access class-level data
        return cls.school_name
        
print(Student.get_school_name()) # O/P: Global Academy
print(Student("Tim").get_school_name()) # O/P: Global Academy
```
# ☱Static methods (`@staticmethod`)
A **static method** is a method that is __structurally bound to a class's namespace__, but ___DOES NOT___ access or modify the state of either the class or its individual object instances.
It behaves exactly like a plain, isolated function, but lives inside the class because it is contextually related to it.
## ☱Accessing static methods
>[!warning] Static methods can be accessed using:
>+ Classname and object __OUTSIDE__ the class
>+ Classname and `self` __WITHIN__ the class

````tabs
tab: Outside class access
```run-python
class TechStack:
    def __init__(self, primary_language):
        self.primary_language = primary_language

    # This is a static method
    @staticmethod
    def is_valid_language(language):
        # No self, no cls. It's just an isolated utility.
        allowed_languages = ["Python", "Java", "Go", "Rust"]
        return language.capitalize() in allowed_languages

# ☱1. Recommended: Call directly via the Class
is_valid = TechStack.is_valid_language("python")
print(is_valid)  # Output: True

# ☱2. Allowed, but can obscure that the method is independent of instance data:
stack = TechStack("Go")
print(stack.is_valid_language("C++"))  # Output: False
```

tab: Inside class access
```run-python
class Calculator:
    @staticmethod
    def add(a, b):
        return a + b
    """If a subclass inherits from Calculator and overrides the add behavior, type(self).add() and cls.add() will correctly invoke the subclass's modified version, whereas Calculator.add() would lock you, to the parent version.
    """
    def instance_process(self, x):
        # Calling static method inside an instance method
        return type(self).add(x, 10)

    @classmethod
    def class_process(cls, x):
        # Calling static method inside a class method
        return cls.add(x, 20)
        
    def instance_process2(self, x):
        # This is allowed, but NOT RECOMMENDED as it gives the impression that `add` is a instance method.
        return self.add(x, 20)
        
c = Calculator()
print(c.instance_process(10))  # O/P: 20
print(c.class_process(10))     # O/PL 30
print(c.instance_process2(10)) # O/P: 30
```
````
# ☱[[Python - Constructors|Constructors]]
# Comparing Objects: `id()` and `isinstance`
# Copying objects
## Shallow copy
## Deep copy
# Dynamic addition of attributes
Python supports dynamic addition of both class and instance attributes.
+ [*] Prevent dynamic addition by using [[#_ _slots _ _ Attribute]].

>[!question]- Why It Is Generally Discouraged?
> + **Reduces Code Readability:** If a developer wants to understand what data a class holds, they look at the `__init__` method or the class-level definitions. If attributes are being injected invisibly from various functions or external files, the code becomes incredibly difficult to read and debug.
> + - **Silent Bug Risks:** If you accidentally misspell a dynamic attribute during assignment (e.g., `user.loctation = "NY"` instead of `location`), Python will gladly create a brand new attribute instead of throwing an error, creating hard-to-track silent bugs.

>[!tip]- When to use?
> - **Metaprogramming and Frameworks:** If you are building a library, an Object-Relational Mapper (ORM) like SQLAlchemy, or a serialization framework (like parsing raw JSON/CSV data directly into objects where the schema isn't known ahead of time).
> - **Quick Prototyping:** When writing quick, throwaway scripts or exploring data in a Jupyter Notebook where speed of execution matters more than strict architecture
## ☱Class attributes
### ☱Globally
```run-python
class Developer:
    pass
# ☱----- Option A: Using Dot Notation ----- 
# ☱Adding a class attribute dynamically
Developer.company = "TechCorp"

# ☱Existing and new instances can access it
alice = Developer()
print(alice.company)  # Output: TechCorp

# ☱----- Option B: Using `setattr` ----- 
attr_name = "industry"
attr_value = "Software"

setattr(Developer, attr_name, attr_value)

print(Developer.industry)  # Output: Software
```
### ☱Instance Method
```run-python
class Developer:
    def __init__(self, name):
        self.name = name  # Regular instance attribute

    def set_global_company(self, company_name):
        # type(self) points to the Developer class itself
        type(self).company = company_name 

# ☱Create two separate instances
alice = Developer("Alice")
bob = Developer("Bob")

# ☱Call the instance method to dynamically add the class attribute
alice.set_global_company("TechCorp")

# ☱Both instances (and the class itself) now share this attribute
print(bob.company)        # Output: TechCorp
print(Developer.company)  # Output: TechCorp
```
### ☱Class method
```run-python
class Robot:
    @classmethod
    def add_global_version(cls, version_number):
        # cls points directly to the Robot class
        cls.version = version_number

# ☱Call the class method directly on the class
Robot.add_global_version("v2.4")

# ☱All future or existing instances can access it
chappie = Robot()
print(chappie.version)  # Output: v2.4
print(Robot.version)    # Output: v2.4
```
## ☱Instance attributes
### ☱Globally
````tabs
tab: Using Dot Notation
```run-python
class Robot:
    pass

chappie = Robot()
wall_e = Robot()

# ☱Dynamically adding an attribute ONLY to chappie
chappie.weapon = "Laser"

print(chappie.weapon)  # Output: Laser
print(wall_e.weapon)  # Raises AttributeError: 'Robot' object has no attribute 'weapon'

tab: Using `setattr`
```run-python
class Robot:
    pass

chappie = Robot()
wall_e = Robot()

# ☱Dynamically adding an attribute ONLY to chappie
setattr(chappie, 'weapon', 'Laser')

print(chappie.weapon)  # Output: Laser
print(wall_e.weapon)  # Raises AttributeError: 'Robot' object has no attribute 'weapon'
```
````
### ☱Instance method
```run-python
class Gamer:
    def __init__(self, username):
        self.username = username  # Set at creation

    def achieve_level_up(self):
        # Dynamically adding an instance attribute later in game
        self.rank = "Pro"  
        
        # Alternatively using setattr()
        setattr(self, "badge", "Gold")

# ☱Usage
player1 = Gamer("PixelKnight")

# ☱player1 does not have a rank yet
print(player1.__dict__)  # Output: {'username': 'PixelKnight'}

player1.achieve_level_up()

# ☱player1 now has the new instance attributes
print(player1.__dict__)  # Output: {'username': 'PixelKnight', 'rank': 'Pro', 'badge': 'Gold'}
```
### ☱Class method
```run-python
class Robot:
    def __init__(self, name):
        self.name = name

    @classmethod
    def upgrade_robot(cls, robot_instance):
        # We target the passed-in instance explicitly
        robot_instance.hardware_version = "v2.0"

# ☱Usage
chappie = Robot("Chappie")
Robot.upgrade_robot(chappie)

print(chappie.hardware_version)  # Output: v2.0
```
# ☱\_\_dict\_\_ Attribute
Both classes and objects have a special attribute \_\_dict\_\_, which returns a [[Python - Dictionary|dictionary]] containing all the methods and variables belonging to that class/object.
# ☱\_\_slots\_\_ Attribute
+ [*] `__slots__ ` is a [[#☱Class Attributes|class attribute]]
+ [p] __Reduce memory footprint__ by preventing creation of \_\_dict\_\_ attribute.
+ [p] Fetching or changing attributes bypasses dictionary lookups, __making access faster__
+ [p] Restrict attributes to a fixed set, preventing [[#Dynamic addition of attributes|dynamic addition of attributes]]. 

```run-python
class ImmutablePoint:
    # Only 'x' and 'y' are allowed. No __dict__ will be created.
    __slots__ = ("x", "y") 
    
    def __init__(self, x, y):
        self.x = x
        self.y = y

pt = ImmutablePoint(10, 20)

# ☱Trying to add a dynamic attribute will crash intentionally
pt.z = 30  
# ☱Raises AttributeError: 'ImmutablePoint' object has no attribute 'z'
```

+ [c] Child subclasses must redeclare `__slots__` manually to stay optimized. If a child omits it, Python restores the memory-heavy `__dict__`.
+ [c] Omitting `__dict__` can disrupt external packages that rely on structural reflection. This includes certain serializers, debuggers, or default `pickle` utilities

# Encapsulation 
Python does not enforce encapsulation strictly. Instead, it has naming conventions for different levels of access.
	This applies to both variables and methods.
## ☱Public
By default, any method you define without leading underscores is **public**.
+ [I] `radius`, `calculate_area()`
## ☱Protected
To mark a method/variable as protected (intended for internal use within the class and its subclasses), **prefix the name with a single underscore**.
+ [I] `_radius`, `_calculate_area()`

>[!warning] Python will **not** raise an error if you try to call a protected method/variable from outside the class.
## ☱Private 
If you want to strongly hide a method and prevent subclasses from accidentally overriding it, **prefix its name with double leading underscores**. This triggers a mechanism called **Name Mangling**.
	Python automatically prepends `_<classname>` to it i.e. the fully qualified name of the variable becomes `_<classname>__<var/methodname>`.
+ [I] `__radius` $\rightarrow$ `_Circle__radius` or `__calculate_area()` $\rightarrow$ `_Circle__calculate_area()`.

>[!warning] This makes it harder to access private variables but does not prevent it. They can still be accessed using their mangled names.
>```python
>>>> sample_instance._SampleClass__value
'Hello!'
>>>> sample_instance._SampleClass__method()
Hello!
>```
## ☱Property attributes
Encapsulation can also be performed using [[Python - Property Attributes|property attributes]]
# ☱Abstraction
Implemented using [[Python - Abstract Base Class|Abstract Base classes]]
# ☱Polymorphism
1. [[Python - Duck typing|Duck typing]]
2. [[Python - Functions#Method Overloading|Method Overloading]]
3. [[Python - Inheritance#Method overriding|Method overriding]] 
# ☱Inheritance
See [[Python - Inheritance]] for more info
# ☱[[Python - Data Classes|Data class]]

# References
[Python classes](https://realpython.com/python-classes/)