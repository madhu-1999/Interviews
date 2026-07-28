---
tags:
  - "#python"
---
# How is a new object created?
A object instantiation call (`o = new Class()`), triggers:
1. Creation of new instance of target class (`.__new__()`)
2. The newly created instance (`self`) is initialized by `.__init__()` with its initial state.

# Object creation with `.__new__()`

We typically, don't have to [](Python%20-%20Inheritance.md#Method%20overriding|override) this method to create a new instance of a class. The `.__new__()` method of the object super class is called implicitly.

```python
class SomeClass:
    def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)
        # Customize your instance here...
        return instance
```

# Initializing with `.__init__()`

If a class does not define a `.__init__()` method, the `.__init__()` of it's [](Python%20-%20Inheritance.md#`super()`%20method|super%20class) is called.
```run-python
class Example:
    def show(self):
        print('There is nothing to show')

# .__init__() of object superclass called implicitly
o = Example()
o.show() # There is nothing to show
```

>[!warning] `.__init__()` ___MUST___ return `None` , else a `TypeError` is thrown!

Once a class defines a `.__init__()` method, the `.__init__()` method of its super class must be called explicitly, else any attributes of the super class will not be initialized.
+ [*] `super()` returns a temporary object of the super class i.e. only `.__new__()` has been called. Since `.__init__()` is not called, its attributes are not initialized and thus, for all intents and purposes do not exist!
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

>[!faq] Can `.__init__()` be overloaded?
>Since Python supports [](Python%20-%20Functions.md#Method%20Overloading|method%20overloading) differently, it is not possible to have two different method signatures for `.__init__()` . The second `.__init__()`  _hides_ the first one i.e. first method cannot be accessed!
>We can use [](Python%20-%20Functions.md#Parameter%20Types|keyword,%20default%20and%20variable%20length%20parameters) to simulate method overloading.

# References
[Python constructors](https://realpython.com/python-class-constructor/)