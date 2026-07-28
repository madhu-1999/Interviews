---
tags:
  - "#python"
---
# Defining a function
```python
def add(a:int, b:int) -> int:
	return a+b
```

>[!info] In Python, everything except [exceptions](Python%20-%20Exceptions.md) are objects. i.e. functions are also objects.

Occasionally, you may want to define a function that does nothing. This is referred to as a **stub**, which is usually a temporary placeholder for a Python function that will be fully implemented at a later time.
```python
def function():
	pass
```

>[!important] Variables defined in a function are [](Python%20-%20Variable%20Scopes.md#Local|local) in scope.
>
# Returning values
## Return nothing
+ [i] Analogous to `void` in Java

>[!warning] Functions with no explicit `return` , return `None`.
## Return single value
```python
def add(a:int, b:int) -> int:
	return a+b
```
## Return multiple values

>[!info] When multiple values are returned, they are automatically enclosed in a [tuple](Python%20-%20Tuples.md).

```run-python
def get_user_profile():
    name = "Alice"
    age = 30
    role = "Engineer"
    return name, age, role  # Python implicitly wraps this as (name, age, role)

# Unpacking into separate variables
user_name, user_age, user_role = get_user_profile()
print(user_name)  # Output: Alice
```

When returning more than 2 or 3 values, positional tuples can become confusing. Returning a [dictionary](Python%20-%20Dictionary.md) / named tuple #cite  / object instead, provides explicit key names
```run-python
def calculate_metrics(numbers):
    return {
        "total": sum(numbers),
        "count": len(numbers),
        "average": sum(numbers) / len(numbers)
    }

metrics = calculate_metrics([10, 20, 30])
print(metrics["average"])  # Output: 20.0

```
# Parameter Types
>
## Default Parameter
+ Parameter in a function definition that have a predefined value. 
+ If no value is provided for this parameter in the function call, it used the default value defined. Ex: `round(x)` function defaults to limiting decimal places to 0.
```python
def calculate_area(length, width=5):
    return length * width
print(calculate_area(4)) # 20
print(caclulate_area(4,3)) # 12
```
## Keyword Parameter
+ Parameters that are named while calling the function.
+ Order does not matter
```python
def calculate_area(length, width):
    return length * width

print(calculate_area(width = 5, length = 4)) # 20
```
## Positional Parameter
+ Parameter passed without naming when calling the function.
+ Order matters
>[!warning] These must be passed before any keyword arguments

```python
def calculate_area(length, width):
    return length * width
print(calculate_area(4, 3)) # 12 length=4, width=3
```
## Variable length arguments 
+ `*args ` : Non keyword arguments passed into a function as a tuple
>[!warning] This must come after positional and keyword parameters
```run-python
from typing import Tuple

def add(*args: Tuple[int,...]) -> int:
	add_val=0
	for arg in args:
		add_val+=arg
	return add_val

numbers = [1, 2, 3]
print(add(2,3)) # 5
print(add(4, 5, 5)) # 10
print(add(*numbers)) # Unpacking `numbers` iterable
```
+ `**kwargs` : Keyword arguments passed into a function as a dict
>[!warning] This must come after positional and keyword parameters
```python
def fun(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")


fun(course="DSA", platform="GeeksforGeeks", difficulty="easy")
# course: DSA
# platform: GeeksforGeeks
# difficulty: easy

numbers = {"one": 1, "two": 2, "three: 3"}
print(fun(**numbers)) # unpacking `number` variable 
```
## Lambda Function Arguments
>[!warning] Lambda functions in Python are limited to being one - liner functions.

+ Single argument
```python
square = lambda x: x**2
print(square(5)) # 25
```
+ Multiple arguments
```python
add = lambda a, b: a+b
print(add(5,4)) # 9
```

# Method Overloading
Function overloading, as done in statically typed languages is not possible in Python.
```run-python
def greet():
    return 'Hi';

def greet(name):
    return f'Hi {name}';
    
print(greet('Maria')) # O/P: Hi Maria
print(greet()) # This will throw TypeError
```
Python is a dynamically typed language, so it does not enforce types or number of arguments we can pass to a function (as evidenced by the use of keyword/default/variable length arguments).
When we create `greet(name)` it hides the function `greet()`.

>[!faq] How does Python support method overloading?

+ [*] Method overloading is supported by the flexibility provided by [keyword](#Keyword%20Parameter), [default](#Default%20Parameter) and [variable length arguments](#Variable%20length%20arguments). Unlike statically typed languages, we have a single method signature but we can pass a varying number of arguments to the function.
+ [*] By using the `multipledispatch` library (3rd party), we can mimic method overloading, as done in statically typed languages i.e. Python will enforce type and number of arguments.
```python
from multipledispatch import dispatch  
  
# Define overloaded methods using the @dispatch decorator  
@dispatch(int, int)  
def product(first, second):  
	return first * second  
  
@dispatch(str, str)  
def product(first, second):  
	return f"{first} {second}"  
  
@dispatch(int, str)  
def product(first, second):  
	return f"{' '.join([second] * first)}"  
  
# Example usage  
print(product(5, 6)) # Output: 30  
print(product("Hello", "World")) # Output: "Hello World"  
print(product(3, "Python")) # Output: "Python Python Python"
```
# Pass by Reference or Pass by Value?
+ Pass by reference for **mutable** types and pass by value for **immutable** types in general.
+ When we pass arguments of type `int`, `float`,  `str` or `tuple`, they do not mutate since they are ***IMMUTABLE***. If reassignment happens, a new object is created.
```python
def square(x:int) -> int:
	# Assume x points to same memory location as num
	x = x**2 # Now, x points to a different memory location
num = 3
square(num)
print(num) # 3

# If we want it to change, return the value and assign it
def square(x:int) -> int:
	x = x**2
	return x
num = square(num)
print(num) # 9
```
+ When we pass arguments of types that are mutable, like `list`, they mutate if the operation is inplace.
```python
def augment_twice(a_list, val):  
    """Put `val` on the end of `a_list` twice."""  
    a_list.append(val)  # append function is inplace
    a_list.append(val)  
  
nums = [1, 2, 3]  
augment_twice(nums, 4)  
print(nums)         # [1, 2, 3, 4, 4]
```
Before appending to a_list:![Screenshot 2025-04-26 at 2.38.29 PM](Assets/Screenshot%202025-04-26%20at%202.38.29%20PM.png)
After appending: ![Screenshot 2025-04-26 at 2.40.26 PM](Assets/Screenshot%202025-04-26%20at%202.40.26%20PM.png)
+ Here is an example where list does not mutate on appending to list
```python
def augment_twice_bad(a_list, val):  
    """Put `val` on the end of `a_list` twice."""  
    a_list = a_list + [val, val]  # + operator creates new list i.e. not inplace
  
nums = [1, 2, 3]  
augment_twice_bad(nums, 4)  
print(nums)         # [1, 2, 3]
```
Before appending: ![Screenshot 2025-04-26 at 2.43.06 PM 1](Assets/Screenshot%202025-04-26%20at%202.43.06%20PM%201.png)
After appending![Screenshot 2025-04-26 at 2.44.15 PM](Assets/Screenshot%202025-04-26%20at%202.44.15%20PM.png)
# [ Generator Functions](Python%20-%20Generators)
# Lambda Functions
They are anonymous functions (i.e. no function name) with some limitations:
+ A lambda function ___CANNOT___ contains statements like `pass` , `return` , `raise`.
+ It defines a __single expression__ in its body, unlike Java which allows multiline lambdas.
>[!info] Lambdas support use of different [#Parameter Types](#Parameter%20Types) .
## Use cases
### With `map()` / `filter()` / `reduce`
```run-python
my_list = [1, 5, 4, 6, 8, 11, 3, 12]  
new_list = list(filter(lambda x: (x%2 == 0) , my_list))  
 
# O/P: [4, 6, 8, 12]
print(new_list)
```
### Key Functions
They are higher-order functions that take a parameter `key` as a named argument. `key` receives a function that can be a `lambda`. Some examples are `sort()`, `sorted()`, `min()`, `max()` etc..

```run-python
ids = ['id1' ,'id2' ,'id30' , 'id3' , 'id22' , 'id100']
# Sort by integer value in string
print(sorted(ids, key=lambda x: x[2:]))
# O/P: ['id1', 'id100', 'id2', 'id22', 'id3', 'id30']
```
## Advantages
+ [p] They __eliminate the boilerplate code__ of defining a full function for a minor, one-off task.
+ [p] You can inject logic directly inside higher-order function arguments, such as `map()`, `filter()`, or `sorted()` ( `key` param) without polluting your local namespace with throw-away function names.
+ [p] They can remember and access variables from their [](Python%20-%20Variable%20Scopes.md#Enclosing|enclosing%20scope).
```run-python
pairs = [(1, 'one'), (2, 'two'), (3, 'three')]
pairs.sort(key=lambda pair: pair[1]) 
print(pairs) # O/P: [(1, 'one'), (3, 'three'), (2, 'two')]
```
## Disadvantages
+ [c] They cannot contain multiple lines of code, loops, or statements.
+ [c] If complex logic is used in a single line, it reduces readability of the code.
+ [c] Because they are anonymous, any error raised inside a lambda displays as `<lambda>` in the stack trace, making __debugging significantly harder__ than identifying a clearly named standard function.
+ [c] Cannot attach docstrings, type hints, or annotations to a lambda expression, limiting the ability to document the code cleanly.
# Inner Functions
We can define a function within another function. This type of function can access names defined in the [](Python%20-%20Variable%20Scopes.md#Enclosing|enclosing%20scope).
+ [!] They can only be called within the outer function.
## Use cases
### Encapsulation
If a function is only needed as a helper inside one specific place, nesting it completely hides it from the global scope. This prevents name clashes and protects the inner function from being called accidentally elsewhere.
```run-python
def increment(number):
    def inner_increment():
        return number + 1
    return inner_increment()

print(increment(10))  # Returns 11

# Trying to call it outside will fail:
inner_increment() # O/P: NameError: name 'inner_increment' is not defined
```
### Code reuse
When you have a long function with a repetitive chunk of code, you can pack that repetitive logic into an inner function to make the main function cleaner and easier to read.
```run-python
def process_user_profiles(raw_users):
    """Cleanses and structures raw user profiles."""
    
    # Reusable inner function to format strings safely
    def clean_string(value):
        if not value:
            return "Unknown"
        return str(value).strip().title()

    processed_profiles = []
    
    for user in raw_users:
        # Reusing 'clean_string' for multiple different fields
        clean_first = clean_string(user.get("first_name"))
        clean_last = clean_string(user.get("last_name"))
        clean_city = clean_string(user.get("city"))
        
        processed_profiles.append({
            "full_name": f"{clean_first} {clean_last}",
            "location": clean_city
        })
        
    return processed_profiles

# Example Usage
dirty_data = [
    {"first_name": "  alice ", "last_name": "smith", "city": "san jose"},
    {"first_name": None, "last_name": "DOE", "city": "  NEW YORK  "}
]

print(process_user_profiles(dirty_data))
# Output: [{'full_name': 'Alice Smith', 'location': 'San Jose'}, {'full_name': 'Unknown Doe', 'location': 'New York'}]
```
### Creating closures
A **closure** happens when an outer function returns the inner function object _without calling it_. The inner function retains a _snapshot_ of the outer function's environment, allowing it to remember variables across consecutive calls.
```run-python
def generate_power(exponent):
    def power(base):
        return base ** exponent
    return power  # Returns the function object

# Retaining unique states
square = generate_power(2)
cube = generate_power(3)

print(square(4))  # 16 (remembers exponent=2)
print(cube(4))    # 64 (remembers exponent=3)
```
## Building Decorators
[Decorators](Decorator%20Pattern.md) are functions that take another function as an argument and extend its behavior without modifying the original code. They heavily rely on inner functions to wrap the original callable and unpack arguments
```run-python
def debug(func):
    def _debug(*args, **kwargs):
        result = func(*args, **kwargs)
        print(f"{func.__name__}(args: {args}) -> {result}")
        return result
    return _debug

@debug
def add(a, b):
    return a + b

add(5, 6)  
# Output: add(args: (5, 6)) -> 11
```
# main() function
+ Used when file is imported into another as a module or executed as a script.
```python
def main():
    print("Hello World!")

if __name__ == "__main__":
    main()
```
+ Each file has a special variable `__name__` defined. The value of which depends on the context file is executed in.
+ `__name__ = __main__` when read from standard input, a script, an interactive prompt or when imported into another module.
+ The name `main()` for the function is a convention.
# References
[Python functions](https://realpython.com/defining-your-own-python-function/)
[Arguments in Python](https://builtin.com/software-engineering-perspectives/arguments-in-python)
[Polymorphism in Python](https://medium.com/data-bistrot/polymorphism-in-python-object-oriented-programming-c652d8c3b792)
[Python lambdas](https://realpython.com/python-lambda/)\