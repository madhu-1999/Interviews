---
tags:
  - "#python"
---
# Overview
+ [i] An __exception__ error occurs whenever syntactically Python code results in an error.
+ [i] All exceptions are derived from the `BaseException` class .
	+ [!] But, custom exceptions must always derive from `Exception` class. 
+ [i] Raise an exception using `raise` statement. 
# Exception Hierarchy
The `BaseException` class is the top-level class in the exception hierarchy. It provides some common methods that all exceptions can use, such as `__str__` and `__repr__`.
However, you should not use `BaseException` directly in your code as it is too broad and can catch any type of exception (ex: `KeyboardInterrupt`, `SystemExit`). 
We should instead, derive from `Exception` or its subclasses.
+ [!] `Exception` is a subclass of `BaseException`.
# Exception Handling
## `try` - `except` 

```run-python
try:
    with open("file.log") as file:
        read_data = file.read()
except FileNotFoundError as fnf_error:
    print(fnf_error)
```
## `try` - `except` - `else`

If the code in `try` block raises no exceptions, then code in `else` block is executed.
```run-python
number = 1
try:
	if number > 5:
		raise Exception
except Exception as e:
	print(e)
else: 
	print(number)
```
## Cleaning up with `finally`
The `finally` block runs regardless of if an exception was raised in the `try` block.
```run-python
number = 1
try:
	if number > 5:
		raise Exception
except Exception as e:
	print(e)
else: 
	print(number)
finally:
	print('Cleaning up')
# O/P: 
# 1
# Cleaning up
```
# Raising an exception
```run-python
number = 10
if number > 5:
    raise Exception(f"The number should not exceed 5. ({number=})")
print(number)
```
# Creating custom exceptions
```python
class PlatformException(Exception):
    """Incompatible platform."""
	pass
```
# References
[Exception Hierarchy](https://profound.academy/python-mid/exception-hierarchy-phonbKOpXJ362GhumAsG)