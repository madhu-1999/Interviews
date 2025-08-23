#python 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Function Definition
+ `def` keyword is used to define a function.
+ Defining a function creates a **function object**.
```python
def add(a:int, b:int) -> int:
	return a+b
```
+ Variables and arguments defined in a function are **local**.
+ They can return multiple values
```python
def op(a:int, b:int) -> int:
	return a+b, a-b, a*b

a, b = 4, 3
add, sub, mult = op(a, b)
```
+ They can also not return any value
```python
def printf():
	print('Hi)
printf()
```
# Argument Types
## Default Arguments
+ Arguments in a function definition that have a predefined value. 
+ If no value is provided for this argument in the function call, it used the default value defined. Ex: `round(x)` function defaults to limiting decimal places to 0.
```python
def calculate_area(length, width=5):
    return length * width
print(calculate_area(4)) # 20
print(caclulate_area(4,3)) # 12
```
## Keyword Arguments
+ Arguments that are named while calling the function.
+ Order does not matter
```python
def calculate_area(length, width):
    return length * width

print(calculate_area(width = 5, length = 4)) # 20
```
## Positional Arguments
+ Arguments passed without naming when calling the function.
+ Order matters
+ These must be passed before any keyword arguments
```python
def calculate_area(length, width):
    return length * width
print(calculate_area(4, 3)) # 12 length=4, width=3
```
## Variable length arguments 
+ `*args ` : Non keyword arguments passed into a function as a tuple
```python
def add(*args: Tuple[int,...]) -> int:
	add_val=0
	for arg in args:
		add_val+=arg
	return add_val

print(add(2,3)) # 5
print(add(4, 5, 5)) # 10
```
+ `**kwargs` : Keyword arguments passed into a function as a dict
```python
def fun(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")


fun(course="DSA", platform="GeeksforGeeks", difficulty="easy")
# course: DSA
# platform: GeeksforGeeks
# difficulty: easy
```
## Lambda Function Arguments
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
+ Achieved using [[Functions#Default Arguments|default arguments]] and [[Functions#Variable length arguments|variable length arguments]].
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
Before appending to a_list:![[Screenshot 2025-04-26 at 2.38.29 PM.png]]
After appending: ![[Screenshot 2025-04-26 at 2.40.26 PM.png]]
+ Here is an example where list does not mutate on appending to list
```python
def augment_twice_bad(a_list, val):  
    """Put `val` on the end of `a_list` twice."""  
    a_list = a_list + [val, val]  # + operator creates new list i.e. not inplace
  
nums = [1, 2, 3]  
augment_twice_bad(nums, 4)  
print(nums)         # [1, 2, 3]
```
Before appending: ![[Screenshot 2025-04-26 at 2.43.06 PM 1.png]]
After appending![[Screenshot 2025-04-26 at 2.44.15 PM.png]]
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