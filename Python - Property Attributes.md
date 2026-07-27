---
tags:
  - "#python"
  - "#oop"
---
# Prerequisite
[[Python - Classes and Objects]]
[[Python - Functions]]
# Overview
It is a special kind of attribute that allows you to add function-like behavior (like data validation, logging, or caching) on top of regular [[Python - Classes and Objects#Instance Attributes|instance attributes]].
+ [p] **Encapsulation & Validation:** You can shield the internal state of your object (stored in the non-public `_radius`) and protect it against invalid data or corruption.
- [p] **Computed Attributes:** You can create properties like `area` that are calculated dynamically on demand, ensuring the value is never stale or out of sync.    
- [p] **Backward Compatibility:** If you start with a simple public attribute like `self.radius = 10` and realize months later that you need to add validation, you can turn it into a `@property`. **None of the existing code using your class will break**, because the calling syntax remains exactly the same
# Creating property attributes
To create a property, Python provides a built-in `@property` decorator. A complete property can have three parts:
1. **Getter:** Retrieves the value (this is the main `@property`).
2. **Setter:** Validates and sets the value (using `@attribute_name.setter`).
3. **Deleter:** Handles cleanup when the attribute is deleted (using `@attribute_name.deleter`)
==By convention, the actual underlying variable is prefixed with an underscore (e.g., `_attribute_name`)==.
```python
class Circuit:
    def __init__(self, voltage):
        # Calls the setter automatically during initialization
        self.voltage = voltage 
        self.current = 10 

    # 1. Getter: Defines how to read the property
    @property
    def voltage(self):
        print("Getting voltage...")
        return self._voltage

    # 2. Setter: Defines how to change the property (adds validation)
    @voltage.setter
    def voltage(self, value):
        print(f"Setting voltage to {value}...")
        if value < 0:
            raise ValueError("Voltage cannot be negative!")
        self._voltage = value

    # 3. Deleter: Defines what happens when 'del' is called
    @voltage.deleter
    def voltage(self):
        print("Deleting voltage...")
        del self._voltage
        
    # Computed attribute
    @property
    def power(self):
	    return self._voltage * self.current

```
# Accessing property attributes
```python
# Instantiating the object triggers the setter
c = Circuit(12)      # Prints: Setting voltage to 12...

# Triggering the getter
print(c.voltage)     # Prints: Getting voltage...12

# Triggering the setter validation logic
try:
    c.voltage = -5   # Throws: ValueError: Voltage cannot be negative!
except ValueError as e:
    print(e)

# Triggering the deleter
del c.voltage        # Prints: Deleting voltage...
```
# Read-only properties
```run-python
class Point:
    def __init__(self, x, y):
        self._x = x
        self._y = y

    @property
    def x(self):
        """Read-only getter for the x coordinate."""
        return self._x

    @property
    def y(self):
        """Read-only getter for the y coordinate."""
        return self._y

# Usage
point = Point(10, 20)
print(point.x)  # Output: 10

# Attempting to mutate a read-only property causes an error
point.x = 42    
# Raises AttributeError: can't set attribute
```
# Write-Only Properties
```run-python
import hashlib
import os

class User:
    def __init__(self, username, plaintext_password):
        self.username = username
        self.password = plaintext_password  # Triggers the setter seamlessly

    @property
    def password(self):
        """Getter explicitly blocks read access."""
        raise AttributeError("Password security policy: password is write-only.")

    @password.setter
    def password(self, plaintext):
        """Setter safely obfuscates and stores the data."""
        salt = os.urandom(32)
        # Hash the password so the plaintext is never stored anywhere
        self._hashed_password = hashlib.pbkdf2_hmac(
            "sha256", plaintext.encode("utf-8"), salt, 100_000
        )

# Usage
user = User("john_doe", "super_secret_123")

# 1. Setting a new value works perfectly
user.password = "new_password_999" 

# 2. Attempting to read it fails safely
print(user.password)
# Raises AttributeError: Password security policy: password is write-only.
```
# Overriding properties in subclasses
+ [*] If you override only one part of a property (like the getter), you completely lose the other parts (like the setter or deleter) from the parent class.
```run-python
class Person:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        self._name = value

class Employee(Person):
    @property
    def name(self):
        # Using super() to fetch the parent's property value
        return super().name.upper()
        
emp = Employee("Alice")
print(emp.name)  # Output: ALICE

# This will fail drastically!
emp.name = "Bob"  
# Raises AttributeError: can't set attribute
```

>[!faq] How to correctly override?
>If you know your properties will be inherited and customized, a cleaner design pattern is to avoid putting logic directly into the property decorators. Instead, route your properties through standard instance methods

```run-python
class Person:
    def __init__(self, name):
        self._name = name

    # Properties point to regular methods
    @property
    def name(self):
        return self._get_name()

    @name.setter
    def name(self, value):
        self._set_name(value)

    # Core logic inside standard, overridable methods
    def _get_name(self):
        return self._name

    def _set_name(self, value):
        self._name = value


class Employee(Person):
    # Overriding just the getter helper method
    def _get_name(self):
        return self._name.upper()
    
    # The setter helper is safely inherited from Person without breaking!

emp = Employee("Alice")
print(emp.name)  # Output: ALICE

emp.name = "Bob"  
print(emp.name)  # Output: BOB

```
# When to use properties?
## Validate before assignment
Instead of writing code to validate a value before assignment, each time we assign a value to an attribute, we can convert the attribute into a property and write validation code only once.

```python
class User:
    def __init__(self, age):
        self.age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Age cannot be negative")
        self._age = value
```
## Create derived attributes
When an attribute depends on other changing values, a property computes the value on the fly. This ensures data never goes out of sync.
```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * (self.radius ** 2)
```
## Enforcing [[#Read-only properties|read-only]] or [[#Write-Only Properties|write only]] attributes
```python
class SystemStatus:
    def __init__(self, build_version):
        self._build_version = build_version

    @property
    def build_version(self): # No setter means it's read-only
        return self._build_version

```
## Maintaining Backward Compatibility
If you initially designed a class with a standard public attribute (`obj.value`), but later need to add logic, logging, or caching, you can turn it into a `@property`. 
**No external code will break** because the access syntax remains exactly the same (`obj.value` instead of transitioning to `obj.get_value()`)
# When NOT to use properties?
+ **Expensive operations**: Database queries, network requests, or heavy loops should be normal methods (e.g., `obj.fetch_data()`). Users expect attribute access (`obj.value`) to be nearly instantaneous
+ **Simple internal attributes**: If you don't need validation, logging, or computations, just use standard public attributes. Overusing properties introduces unnecessary boilerplate.
# References
[Python properties](https://realpython.com/python-property)
