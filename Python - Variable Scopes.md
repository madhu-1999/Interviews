#python 
# LEGB Rule
Python resolves names using the LEGB rule, which defines the order in which the interpreter searches through distinct scopes.
	**L**ocal (First)
	**E**nclosing
	**G**lobal
	**B**uilt-in (Last)
# Local
Variables inside a [[Python - Functions|function]] or lambda expression.
If we try to access local variables outside the function/lambda, a **NameError** exception is thrown.
You can inspect the names in a function’s local scope using the built-in [`dir()`](https://realpython.com/ref/builtin-functions/dir/) and [`vars()`](https://realpython.com/ref/builtin-functions/vars/) functions:
# Enclosing
Variables defined in nested functions fall under enclosing/nonlocal scope.
+ Variables defined in outer function are accessible in inner function. 
	+ [!] Exception: Variables defined in outer function after calling inner function are ***NOT*** accessible.
+ Variables defined in inner function are ***NOT*** accessible inside outer function. 
````tabs
tab: Example 1
```run-python
def outer_func():
	# Local to outer_func() and nonlocal to inner_func()
	var = 100
	def inner_func():
		print(f"var from inner_func: {var}")
	inner_func()
	print(f"var from outer_func: {var}")
outer_func()
# O/P:
# var from inner_func: 100 
# var from outer_func: 100
```
tab: Exception
```run-python
def outer_func():
	# Local to outer_func() and nonlocal to inner_func()
	var = 100
	def inner_func():
		print(f"var from inner_func: {var}")
		print(f"var1 from inner_func: {var1}")
		print(f"another_var from inner_func: {another_var}")
	var1 = 200
	inner_func()
	another_var = 300
	print(f"var from outer_func: {var}")
outer_func()
# O/P:
# var from inner_func: 100 
# var1 from inner_func: 200
# cannot access free variable 'another_var' where it is not associated with a value in enclosing scope
```
````
## Updating nonlocal variables
+ [*] `nonlocal`  ***CANNOT*** be used outside of a nested or inner function 
````tabs
tab: ❌ Incorrect
```run-python
def outer_func():
	number = 42
	def inner_func():
		number = 21 # This creates local variable 
		return number
	inner_func()
	print(f"number: {number}") # O/P: 42
outer_func()
```
tab: ✅ Correct
```run-python
def outer_func():
	number = 42
	def inner_func():
		nonlocal number
		number = 21
		return number
	inner_func()
	print(f"number: {number}") # O/P: 21
outer_func()
```

tab: ⭐️ Recommended
```run-python
global number = 42
def update_number():
	number = 21 # Updates global variable directly
	return number
	
number = update_number()
print(f"number: {number}") # O/P: 21
```
````
# Global
From the moment you launch a Python program, you’re in the global scope.
The global scope remains in existence for the duration of a program’s execution, and all names defined there are accessible throughout that time.
	Internally, Python turns your program’s main script into a module called `__main__` to hold the main program’s execution.
## Updating global variables
````tabs
tab: ❌ Incorrect
```run-python
number = 42
def update_number():
	number = 21 # This creates a local variable number
	return number
	
update_number()
print(f"number: {number}") # O/P: 42
```
tab: ✅ Correct
```run-python
global number = 42
def update_number():
	number = 21 # Updates global variable directly
	return number
	
update_number()
print(f"number: {number}") # O/P: 21
```

tab: ⭐️ Recommended
```run-python
global number = 42
def update_number():
	number = 21 # Updates global variable directly
	return number
	
number = update_number()
print(f"number: {number}") # O/P: 21
```
````
Updating global variables is considered **bad practice**
+ [c] **Difficult to debug:** You can change the value of a global name from almost any top-level code in a program.
- [c] **Hard to understand:** You need to be aware of all the statements that modify global names.
- [c] **Impossible to reuse:** You may not be able to reuse the code because it’s dependent on global names that are specific to a concrete program.
# Built - in 
The **built-in scope** is a special scope that’s implemented as a standard library module named [`builtins`](https://docs.python.org/3/library/builtins.html#module-builtins).
	The names in `builtins` are loaded into your global scope as a mapping with the special name `__builtins__`.
```run-python
import builtins

print(abs(-15))
abs = 20 # abs is now a variable  (shadowing)
print(builtins.abs(-15)) # Works because we explicitly call the function abs
print(abs(-15)) # Throws a TypeError since abs is a variable
```
# References
[Python Scopes](https://realpython.com/python-scope-legb-rule/)