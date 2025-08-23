#python 
# If 
```python
x = 23
if x != 23:
	print(True)
```
# If-else
```python
x = 23
if x > 23:
	print(True)
else: 
	print(False)
```
# If-elif
```python
x = 23
if x > 23:
	print("Greater")
elif x < 23:
	print("Lower")
else: 
	print("Equal)
```
# In
+ To check if specified object is in iterable container like [[List]].
```python
name = "John"
if name in ["John", "Rick"]:
    print("Your name is either John or Rick.")

name = "John"
if name not in ["John", "Rick"]:
    print("Your name is neither John nor Rick.")
```
# Is
+ Matches instances (i.e. same block of memory) , ***NOT*** values
```python
x = [1, 2, 3]
y = [1, 2, 3]
print(x == y) # True (Equality of values)
print(x is y) # False (Two different lists)

x = y = 2
print(x == y) # True (Equality of values)
print(x is y) # Unpredictable
```
+ For `int`, `float`, `str`  **is** operator may return True or False depending on optimization done by Python. If values were cached, then both variables `x` and `y` will point to same memory location and result would be True else False.