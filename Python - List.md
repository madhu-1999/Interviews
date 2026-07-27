---
tags:
  - "#dsa"
  - "#python"
---
# Overview
+ [i] __Ordered__ by insertion order.
+ [i] __Mutable__
+ [i] They can store objects of different types.
+ [i] __[[Python - Iterable and Iterators|Iterable]]__ 
# Creating List
## Using literals
```python
l1 = [1, 2, 3, 4]

l2 = ["Hi", "Bye" , "Hola", "Sayonara"]

# ☱List of functions
l3 = [print, len, enumerate]
```
## Using `list()` 
```run-python
# O/P: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(list((0, 1, 2, 3, 4, 5, 6, 7, 8, 9)))

# O/P: ['pentagon', 'triangle', 'circle', 'rectangle', 'square']
print(list({"circle", "square", "triangle", "rectangle", "pentagon"}))

# O/P: [('name', 'John'), ('age', 30), ('city', 'New York')]
print(list({"name": "John", "age": 30, "city": "New York"}.items()))

# O/P: ['P', 'y', 't', 'h', 'o', 'n', 'i', 's', 't', 'a']
print(list("Pythonista"))

# O/P: []
print(list())
```
## Using [[Python - Comprehensions|List Comprehensions]]
```run-python
#O/P: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
print([number ** 2 for number in range(1, 11)])
```
# Accessing elements
```run-python
l1 = [1, 2, 3, 4]

# Access element
print(l1[1])

# Access element using reverse index
# Last element: -1, Second Last: -2 etc..
print(l1[-2])

# If index is out of range, a `IndexError` is thrown
#print(l1[5])
print(l1[-5])
```
## Slicing
```run-python
l1 = [1, 2, 3, 4, 5, 6, 7, 8]

# Syntax: l1[start:stop:step] 
# Returns list of elements in the range [start, stop)
# Operation does not mutate original list!
print(l1[1:3:1]) # O/P: [2, 3]
print(l1) # O/P: [1, 2, 3, 4, 5, 6, 7, 8]

# start param default value: 0
print(l1[:-6:-2]) # O/P: [8, 6, 4]

# stop param default value: len(l1)
print(l1[2::2]) # O/P: [3, 5, 7]

# step param default value: 1
print(l1[2:6]) # O/P: [3, 4, 5, 6]

# All params optional
print(l1[::]) # O/P: [1, 2, 3, 4, 5, 6, 7, 8]

# What happens if you specify a param value that is out of range for the list object?
####### It returns values within range!
print(l1[:243:6]) # O/P: [1, 7]
```

>[!warning] Slicing creates a shallow copy of the original list, but when we modify any value in the original list, it is NOT reflected in the slice and vice versa.

```run-python
l1 = [1, 2, 3, 4, 5, 6, 7, 8]

l2 = l1[:4]
print(l2) #O/P: [1, 2, 3, 4]

# The lists `l1` and `l2` are different objects
print(id(l1) == id(l2)) # O/P: False

#Since l2 is a shallow copy of l1, their elements are the same object.
print(id(l1[0]) == id(l2[0])) # O/P: True

# But when we modify either l1 or l2, just the modified element is replaced with a different object.
l1[0] = 999
print(l2) # O/P: [1, 2, 3, 4]
print(l1) # O/P:[999, 2, 3, 4, 5, 6, 7, 8]
print(id(l1[0]) == id(l2[0])) # O/P: False
print(id(l1[1]) == id(l2[1])) # O/P: True
```
# Adding elements
```run-python
l1 = [1, 2, 3]

# Adding an element at the end
l1.append(9)
print(l1) # O/P: [1, 2, 3, 9]

# Adding multiple elements at the end
l1.extend([2, 3, 4])
print(l1) # O//P: [1, 2, 3, 9, 2, 3, 4]

# Adding an element at a specific index
l1.insert(1, 999)
print(l1) # O/P: [1, 999, 2, 3, 9, 2, 3, 4]
```
# Updating elements
```run-python
l1 = [1, 2, 3, 4, 5, 6, 7, 8]

# Update an element
l1[0] = 999
print(l1) # O/P: [999, 2, 3, 4, 5, 6, 7, 8]

# Update multiple elements using slices
l1[::2] = [1, 22, 33, 44]
print(l1) # O/P: [1, 2, 22, 4, 33, 6, 44, 8]

# If slice is smaller than elements to update, list GROWS
l1[:2] = [23, 34, 45]
print(l1) # O/P: [23, 34, 45, 22, 4, 33, 6, 44, 8]

# If slice is larger than elements to update, list SHRINKS
l1[:8] = [1, 2, 3, 4, 5]
print(l1) # O/P: 1, 2, 3, 4, 5, 8]
```
# Deleting elements
## `pop()`

```run-python
l1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]

# pop(index) removes and returns element at given index
elem = l1.pop(2)
print(l1) # O/P: [1, 2, 4, 5, 6, 7, 8, 9]
print(f"Deleted element: {elem}") # O/P: Deleted element: 3

# If no index provided, it removes and returns last element.
l1.pop()
print(l1) # O/P: [1, 2, 4, 5, 6, 7, 8]

# If index provided is out of range, it throws a `IndexError`
l1.pop(234)
```
## `remove()`

```run-python
l1 = [1, 2, 3, 4, 2, 5, 4, 2, 9]

# Use remove() if you don't know the index of an element
# Deletes the first occurrence of the given element.
l1.remove(2) 
print(l1) # O/P: [1, 3, 4, 2, 5, 4, 2, 9]

# If provided element is not present in the list, it throws a `ValueError
l1.remove(23)
```
## `clear()`

```run-python
# Use clear() to empty a list
l1 = [1, 2, 3, 4, 2, 5, 4, 2, 9]
l1.clear()
print(l1)
```
## `del` Statement

```run-python
l1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]

# del statement can also be used to delete elements
# Delete a single element
del l1[-1]
print(l1) # O/P: [1, 2, 3, 4, 5, 6, 7, 8]

# Delete multiple elements
del l1[2:4]
print(l1) # O/P: [1, 2, 5, 6, 7, 8]

# Clear a list
del l1[:]
print(l1) # O/P: []
```
# Concatenating Lists
```run-python
l1 = [1, 2, 3, 4]
l2 = [2, 3, 4]

# Using + operator
# This creates a new list
print(l1 + l2) # O/P: [1, 2, 3, 4, 2, 3, 4]

# Using extend()
# This mutates the first list
l1.extend(l2)
print(l1) # O/P: [1, 2, 3, 4, 2, 3, 4]
```
# Repeating the Content of a List
```run-python
l1 = [1, 2, 3]

# This returns a new list that repeats the contents of l1 a given number of times.
print(l1 * 3) # O/P: [1, 2, 3, 1, 2 ,3, 1, 2, 3]
```
# Reversing a list
## `reversed()`

Refer [[Python - Iterable and Iterators|iterator]] for more details.
```run-python
digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Returns a iterator for the reversed list
print(reversed(digits)) # O/P: <list_reverseiterator object at 0x10b261a50>

# Using the `list()` function to convert to a list
print(list(reversed(digits))) # O/P: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

# It does not mutate the original list
print(digits) # O/P: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```
## `reverse()`

```run-python
digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# It mutates the original list
digits.reverse()
print(digits) # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```
## [[#Slicing]]
```run-python
# We can also reverse a list using slicing
digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(digits[::-1]) # O/P: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```
# Sorting a list
+ [!]  Only __homogenous__ lists can be sorted!
## `sorted()`

```run-python
numbers = [2, 9, 5, 1, 6]

# It returns a new list of the sorted numbers in ascending order
print(sorted(numbers)) # O/P: [1, 2, 5, 6, 9]
print(numbers) # O/P: [2, 9, 5, 1, 6]

# This returns a new sorted list in reverse order
print(sorted(numbers, reverse=True)) # O/P: [9, 6, 5, 2, 1]
```

## `sort()`

```run-python
numbers = [2, 9, 5, 1, 6]

# It mutates the list in-place
numbers.sort()
print(numbers) # O/P: [1, 2, 5, 6, 9]

# This sorts the list in reverse order
numbers.sort(reverse=True)
print(numbers) # O/P: [9, 6, 5, 2, 1]
```
## Custom sorting
+ [i] The same syntax can be used in both `sorted()` and `sort()`. 
	Examples below use `sorted()`

We can do custom sorting, using the `key` parameter which takes a lambda function as an argument. 
+ [!] Lambda functions in Python __must__ be a one-liner. 
```run-python
# Sample data: (Name, Grade, Age)
students = [("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10)]

# Sort by Grade ascending 
sorted_students = sorted(students, key=lambda x: x[1])
print(sorted_students)
# O/P: [('Bob', 'A', 15), ('Alice', 'B', 12), ('Charlie', 'B', 10)]

# Sort by Grade descending 
sorted_students = sorted(students, key=lambda x: x[1], reverse=True)
print(sorted_students)
# O/P: [('Alice', 'B', 12), ('Charlie', 'B', 10), ('Bob', 'A', 15)]
```

>[!faq] How to sort on multiple fields?

To sort on multiple fields, we can pass in the fields to sort by, in a tuple.
Here, `x[1]` i.e Grade is the primary sort key and `x[2]` i.e. Age is the secondary sort key.
```run-python
# Sample data: (Name, Grade, Age)
students = [("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10)]

# Sort by Grade (primary), then by Age (secondary) (both ascending)
sorted_students = sorted(students, key=lambda x: (x[1], x[2]))
print(sorted_students)
# Output: [('Bob', 'A', 15), ('Charlie', 'B', 10), ('Alice', 'B', 12)]

# Sort by Grade (primary), then by Age (secondary) (both descending)
sorted_students = sorted(students, key=lambda x: (x[1], x[2]), reverse=True)
print(sorted_students)
# Output: [('Alice', 'B', 12), ('Charlie', 'B', 10), ('Bob', 'A', 15)]
```

>[!faq] But what if i want to sort in ascending order, then descending order or vice versa?

```run-python
# Sample data: (Name, Grade, Age)
students = [("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10)]

# Sort by Grade (Ascending), then Name (Descending)
# Step 1: Sort by secondary key (Name) descending
students.sort(key=lambda x: x[0], reverse=True)

# Step 2: Sort by primary key (Grade) ascending
students.sort(key=lambda x: x[1])
print(students)
# O/P: [('Bob', 'A', 15), ('Charlie', 'B', 10), ('Alice', 'B', 12)]
```

>[!faq] What if my comparison function is more complex than what a lambda function supports?

```run-python
import functools

def mycmp(a, b):
    if len(a) > len(b):
        return 1
    elif len(a) < len(b):
        return -1
    else:
        return 0

a = ["apple", "fig", "banana", "kiwi"]
# Sort list in the ascending order of the length of an element
res = sorted(a, key=functools.cmp_to_key(mycmp))
print(res) # O/P: ['fig', 'kiwi', 'apple', 'banana']
```
# Comparing lists
```run-python
l1 = [2, 3]
l2 = [2, 3]
l3 = [2, 3, 4]
l4 = [2, 3, 9]
# `l1` and `l2` are two different lists
print(id(l1) == id(l2)) # False

# Two lists are equal if same value is at same position in both lists
print(l1 == l2) # O/P: True
print(l1 == l3) # O/P: False

# <, > , <=, >= comparisons are also done on values at same position.
# The comparison happens element by element. If first comparison 
# returns True, we stop, else we compare the second elements etc..
# Here 5 > 2 we stop and return True
print(l4 > l3) # O/P: True

# This is true for lists of unequal lengths.
# Since first two elements match, and `l` has no more elements but `ll` has more elements, `ll` > 'l'
l = [5, 6]
ll = [5, 6, 4]
print(ll > l) # O/P: True
```
# References
[Custom sorting in Python](https://www.geeksforgeeks.org/python/how-does-the-functools-cmp_to_key-function-works-in-python/)
[Python lists](https://realpython.com/python-list/)
# Further reading
[Don't inherit from lists](https://treyhunner.com/2019/04/why-you-shouldnt-inherit-from-list-and-dict-in-python/)
