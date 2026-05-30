#python
+ Python is completely **object-oriented**.
+ **Every variable is an object**.
+ It is ***NOT*** **statically-typed** i.e. no need to declare the type of a variable.
# Types
+ Note that `int`, `float` and `str` are all ***IMMUTABLE*** . i.e. variable (reference) points to a different memory location when value is changed.
+ When two variables of type `int`, `float` or `str` store the same value, they may or may not be pointing to same memory location, depending on the optimization done by python.
## Integers
+ Max size constrained by available memory of a system. Starts off with 32 bits and increases as needed uptil size of available RAM.
+ Integer division using $//$ operator. It always **rounds down**.
```python
print(84//2) #42
```
+ `int()` converts a float to an integer. It **rounds down**.
## Floating-point numbers
+ 64 bit numbers with 15-17 decimal digit precision.
+ `round(x, 2)` used to limit digits after decimal (here limited to 2) .If we call `round(x)` , by default, it limits decimal digits to 0, and returns an `int`.
+ Float division using $/$ operator 
```python
print(84/2) #42.0
```
+ `float()` converts a integer to a float.
## [[Strings]]
+ Defined using ==' '== or ==" "== .
## Bool
+ `True` and `False` are special values that are of type `bool`.
# Operators
+ Follows ***PEMDAS*** (Parentheses, Exponent, Mult, Div, Add,Sub)
+ __**__ is exponentiation operator. Used with numbers (ints and floats).
+ `6**2`  = $6^2$ 
```python
print(6**2) #36
```
+ We can simultaneously assign multiple variables together, variables can be of different types.
```python
a, b, c = 4, 4.5, 'hi'
print(a, b, c) # 4 4.5 hi
```
+ `and`, `or`, `not` are logical operators
# Variable Naming
+ Can contain letters, digits, _ (underscore).
+ ***MUST*** start with a letter or _ (underscore).
+ ***CANNOT*** be a reserved keyword.
>False      await      else       import     pass
None       break      except     in         raise
True       class      finally    is         return
and        continue   for        lambda     try
as         def        from       nonlocal   while
assert     del        global     not        with
async      elif       if         or         yield
# Interesting facts
+ `type()` function used to find out type of a variable. Will return `int` , `float`, `str` depending on variable type.
	+ `type(int)` is of type `type`. Same for `float` and `str`.
	+ `type(A)` is of type `type`, where A is a class.
	+ `type(A())` is of type `A`, where `A()` is an object of class A.
	+ `type(abs)` is of type `builtin_function_or_method` .
	+ `type(add)` is of type `function` , where `add()` is a user defined function
+ Can use _ (single underscore) to make larger numbers easier to read. Numbers are still interpreted as int/float.
```python
num = 100_000_000
print(type(num)) #int
```
+ `int`, `float` , `str` are ***NOT*** reserved keywords. We can create variables/ functions with their names, but doing so overrides the built-in type.
```python
int = 3
print(int(43.0)) #TypeError: 'int' object is not callable
```
+ `math.exp(x)` calculates $e^x$ more accurately than `math.pow(math.e, x)`  because, it does not use approximation of $e$ to do the calculation.