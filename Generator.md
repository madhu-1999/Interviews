#python 

# Iterator
+ Any object with a `__next__` method.
# Iterable
+ Any object with a `__iter__` or `__getitem__` method defined which returns an iterator or supports indexing.
# Generator
+ Special type of iterator that can be **iterated over only once**.
+ It does not store values in memory, instead it generates them on them fly. Thus, it uses less resources.
+ Generators ***DO NOT*** `return` a value, they `yield` the value.
```python
def generator_function():
	for i in range(3):
		yield i

for i in generator_function():
	print(i)
# O/P: 0, 1, 2

gen = generator_function()
print(next(gen)) # 0
print(next(gen)) # 1
print(next(gen)) # 2
print(next(gen)) # StopIteration Error since no next value found
''' The for loop automatically catches this error and stops calling next() so we don't see this error in first example.'''
```
 ```