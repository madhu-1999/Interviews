---
tags:
  - "#python"
  - design-pattern-impl
---
# Overview
+ [i] Iterators implement the [[Iterator Pattern]] 
	+ [I] For loops, [[Python - Comprehensions]]  and iterable unpacking, support iteration using iterators under the hood.
	+ [I] Custom iterators must implement `.__iter__()` to initialize an iterator, and `.__next__()` to return the next value in the data stream.
+ [i] Iterable is any object that implements the `.__iter__()` method or `.__getitem__()` method, and thus, can be __iterated over by an iterator__.
	+ [I] If `.__iter__()` is not specified, Python looks for the `.__getitem__()` method. 
# Custom iterator class
To make a custom iterator class, it must implement two methods:
1. `.__iter__()` : Called to initialize an iterator. It returns an iterator object.
2. `.__next__()` : Called to iterate over the iterator.  It must return the next value in the data stream, and raise a `StopIteration` exception when no more items are available in the data stream.
```run-python
class SequenceIterator:
    def __init__(self, sequence):
        self._sequence = sequence
        self._index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self._index < len(self._sequence):
            item = self._sequence[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration

# Using with for loops
o = SequenceIterator([1, 2, 3, 4])
print('For loop')
for item in o:
	print(item)
	
# Using with while loops
o1 = SequenceIterator([5, 6, 7, 8])
it = iter(o1) # This calls __iter__() internally
print('While loop')
while True:
	try:
		item = next(it)# This calls __next___() internally
		print(item)
	except StopIteration:
		break
```

The `abc` module provides a [[Python - Abstract Base Class|Abstract Base class]] for creating custom iterators, called `Iterator`. It provides a built-in implementation of `.__iter__()` , which means we can skip its implementation, and implement only `.__next__()`.

```run-python
from collections.abc import Iterator

class SequenceIterator(Iterator):
    def __init__(self, sequence):
        self._sequence = sequence
        self._index = 0

    def __next__(self):
        if self._index < len(self._sequence):
            item = self._sequence[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration

# Using with for loops
o = SequenceIterator([1, 2, 3, 4])
print('For loop')
for item in o:
	print(item)
	
# Using with while loops
o1 = SequenceIterator([5, 6, 7, 8])
it = iter(o1) # This calls __iter__() internally
print('While loop')
while True:
	try:
		item = next(it)# This calls __next___() internally
		print(item)
	except StopIteration:
		break
```
# Function based iterators using [[Python - Generators|Generators]]
Unlike regular functions, which typically compute a value and return it to the caller, generator functions return a **generator iterator** that yields a stream of data one value at a time.
```run-python
def sequence_generator(sequence):
    for item in sequence:
            yield item
            
for number in sequence_generator([1, 2, 3, 4]):
    print(number)
```
# Pros and cons of Iterators
+ [p] __Memory Efficient__: Instead of loading an entire collection of data into memory at once, iterators fetch items lazily, or on-demand. This allows Python scripts to process huge datasets or infinite streams with a tiny memory footprint.
+ [p] **Decoupling Algorithms and Data:** Iterators abstract the process of traversing a collection away from the underlying data structure. You can use the same iteration logic (like a `for` loop) for a list, dictionary, or custom object.
+ [c] **Exhaustive (One-Way Flow):** Iterators are "forward-only." Once an item is retrieved, it cannot be accessed again without re-creating the iterator. You cannot look backwards or jump to random indexes.
+ [c] **No Random Access:** Because they are stateless for previous items and generate values on the fly, you cannot retrieve a specific index (like `my_iterator[5]`) without iterating up to that point. []
# Custom iterable class
An **iterable** is an object implementing the `.__iter__()` special method (which must return an iterator object) or the `.__getitem__()` method.
	If `.__iter__()` is not specified, it fallbacks to `.__getitem__()` method to return next element.
```run-python
class SequenceIterator:
    def __init__(self, sequence):
        self._sequence = sequence
        self._index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self._index < len(self._sequence):
            item = self._sequence[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration
            
class Iterable:
    def __init__(self, sequence):
        self.sequence = sequence

    def __iter__(self):
	    # A `SequenceIterator` object is, itself an iterator
        return SequenceIterator(self.sequence) 
        
# For loop calls `.__iter__()` method to get an iterator
for value in Iterable([1, 2, 3, 4]):
    print(value)
```
# Iterator vs Iterable
|Feature|Iterators|Iterables|
|---|---|---|
|Can be used in `for` loops directly|✅|✅|
|Can be iterated over many times|❌|✅|
|Support the `iter()` function|✅|✅|
|Support the `next()` function|✅|❌|
|Keep information about the state of iteration|✅|❌|
|Optimize memory use|✅|❌|
# References
[Python Iterators and Iterables](https://realpython.com/python-iterators-iterables/#understanding-iteration-in-python)