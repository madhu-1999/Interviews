---
tags:
  - "#dsa"
  - "#python"
---
# Overview
+ [i] __Ordered__ by insertion order
+ [i]  __Immutable__
+ [i] They can store objects of different types.
+ [i] __[[Python - Iterable and Iterators|Iterable]]__ 
+ [i] Hashable ( They can be used as keys for a [[Python - Dictionary|dictionary]])
# Creating tuples
>[!warning] A tuple of one element requires a trailing comma to be created
>```run-python
># This is a valid tuple
>l1 = (1,)
>print(l1) # O/P: (1,)
># This is not a valid tuple
>l2 = (1)
>print(l2) # O/P: 1
>```
## Using literals
```python
l1 = (1, 2, 3, 4)

l2 = ("Hi", "Bye" , "Hola", "Sayonara")

# Tuple of functions
l3 = (print, len, enumerate)
```
## Using `tuple()` 
```run-python
# O/P: (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
print(tuple((0, 1, 2, 3, 4, 5, 6, 7, 8, 9)))

# O/P: ('pentagon', 'triangle', 'circle', 'rectangle', 'square')
print(tuple({"circle", "square", "triangle", "rectangle", "pentagon"}))

# O/P: (('name', 'John'), ('age', 30), ('city', 'New York'))
print(tuple({"name": "John", "age": 30, "city": "New York"}.items()))

# O/P: ('P', 'y', 't', 'h', 'o', 'n', 'i', 's', 't', 'a')
print(tuple("Pythonista"))

# O/P: ()
print(tuple())

# We can also create tuples by passing in a generator expression
# O/P: (0, 1, 4, 9, 16)
print(tuple(x**2 for x in range(5))) 
```
# Accessing elements
```run-python
l1 = (1, 2, 3, 4)

# Access element
print(l1[1]) # O/P: 2

# Access element using reverse index
# Last element: -1, Second Last: -2 etc..
print(l1[-2]) # O/P: 3

# If index is out of range, a `IndexError` is thrown
#print(l1[5])
print(l1[-5])
```
## Slicing
```run-python
l1 = (1, 2, 3, 4, 5, 6, 7, 8)

# Syntax: l1[start:stop:step] 
# Returns tuple of elements in the range [start, stop)
# Operation does not mutate tuple
print(l1[1:3:1]) # O/P: (2, 3)
print(l1) # O/P: (1, 2, 3, 4, 5, 6, 7, 8)

# start param default value: 0
print(l1[:-6:-2]) # O/P: (8, 6, 4)

# stop param default value: len(l1)
print(l1[2::2]) # O/P: (3, 5, 7)

# step param default value: 1
print(l1[2:6]) # O/P: (3, 4, 5, 6)

# All params optional
print(l1[::]) # O/P: (1, 2, 3, 4, 5, 6, 7, 8)

# What happens if you specify a param value that is out of range for the list object?
####### It returns values within range!
print(l1[:243:6]) # O/P: (1, 7)
```
# Tuple Immutability
Tuples are immutable, which means that once you’ve created a tuple, you can’t change or update its items in place.
```run-python
jane = ("Jane Doe", 25, 1.75, "Canada")

# This will throw a TypeError
jane[3] = "United States"
```

```run-python
point = (7, 14, 21)

# This will also throw a TypError
del point[2]
```

+ [*] If the tuple stores mutable objects (ex: [[Python - List|list]]) then, the object ==reference cannot be changed== but its ==contents can be changed==.
	+ [!] Tuples with mutable objects are not hashable. i.e they cannot be used as dictionary keys. 
```run-python
student_info = ("Linda", 18, ["Math", "Physics", "History"])
# Since list is mutable, its contents can be changed.
student_info[2][2] = "Computer science"
print(student_info) # O/P: ('Linda', 18, ['Math', 'Physics', 'Computer science'])

# But we cannot replace the list object itself
# This throws a TypeError
student_info[2] = ["Math", "Physics", "Computer science"]
```
# Concatenating Tuples
```run-python
personal_info = ("John", 35)
professional_info = ("Computer science", ("Python", "Django", "Flask"))

# Tuples are immutable, but we can concatenate them together.
# This is because the operation does not mutate the original tuple, it creates a new tuple
# Using the  + operator
new_profile = personal_info + professional_info
print(new_profile)
# O/P: ('John', 35, 'Computer science', ('Python', 'Django', 'Flask'))

# Using tuple unpacking
new_profile = (*personal_info, *professional_info)
print(new_profile)
# O/P: ('John', 35, 'Computer science', ('Python', 'Django', 'Flask'))
```
# Repeating the content of a tuple
```run-python
l1 = (1, 2, 3)

# This returns a new list that repeats the contents of l1 a given number of times.
print(l1 * 3) # O/P: (1, 2, 3, 1, 2 ,3, 1, 2, 3)
```
# Reversing a tuple
+ [!] `reverse()` __CANNOT__ be used, as it mutates the object.

```run-python
digits = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)

# Returns a iterator for the reversed list
print(reversed(digits)) # O/P: <reversed object at 0x102bd7cd0>

# Using the `tuple()` function to convert to a tuple
print(tuple(reversed(digits))) # O/P: (9, 8, 7, 6, 5, 4, 3, 2, 1, 0)

# It does not mutate the original list
print(digits) # O/P: (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```
## [[#Slicing]]
```run-python
# We can also reverse a tuple using slicing
digits = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
print(digits[::-1]) # O/P: (9, 8, 7, 6, 5, 4, 3, 2, 1, 0)
```
# Sorting a tuple
+ [!]  Only __homogenous__ tuples can be sorted!
+ [!] `sort()` __CANNOT__ be used, as it mutates the object.
+ [!] `sorted()` returns a list, we must convert it into a tuple. 

```run-python
numbers = (2, 9, 5, 1, 6)

# It returns a new list of the sorted numbers in ascending order
print(sorted(numbers)) # O/P: [1, 2, 5, 6, 9]
print(numbers) # O/P: (2, 9, 5, 1, 6)

# This returns a new sorted list in reverse order
print(sorted(numbers, reverse=True)) # O/P: [9, 6, 5, 2, 1]
```
## Custom sorting
We can do custom sorting, using the `key` parameter which takes a lambda function as an argument. 
+ [!] Lambda functions in Python __must__ be a one-liner. 
```run-python
# Sample data: (Name, Grade, Age)
students = (("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10))

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
students = (("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10))

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
students = (("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10))

# Sort by Grade (Ascending), then Name (Descending)
# Step 1: Sort by secondary key (Name) descending
sorted_students = sorted(students, key=lambda x: x[0], reverse=True)

# Step 2: Sort by primary key (Grade) ascending
sorted_students = sorted(sorted_students, key=lambda x: x[1])
print(sorted_students)
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

a = ("apple", "fig", "banana", "kiwi")
# Sort list in the ascending order of the length of an element
res = sorted(a, key=functools.cmp_to_key(mycmp))
print(res) # O/P: ['fig', 'kiwi', 'apple', 'banana']
```
# `enumerate()`  with tuples

```run-python
monthly_incomes = (
     ("January", 5000),
     ("February", 5500),
     ("March", 6000),
     ("April", 5800),
     ("May", 6200),
     ("June", 7000),
     ("July", 7500),
     ("August", 7300),
     ("September", 6800),
     ("October", 6500),
     ("November", 6000),
     ("December", 5500)
)

for idx, (month, income) in enumerate(monthly_incomes):
    print(f"{idx}. {month:>10}: {income}")
''' O/P: 
0.    January: 5000
1.   February: 5500
2.      March: 6000
3.      April: 5800
4.        May: 6200
5.       June: 7000
6.       July: 7500
7.     August: 7300
8.  September: 6800
9.    October: 6500
10.   November: 6000
11.   December: 5500
'''
```
# Comparing tuples
```run-python
l1 = (2, 3)
l2 = (2, 3)
l3 = (2, 3, 4)
l4 = (2, 3, 9)

# `l1` and `l2` are the same tuple
# Since tuples are immutable, Python optimizes to reuse tuples
print(id(l1) == id(l2)) # True

# Two tuples are equal if same value is at same position in both tuples
print(l1 == l2) # O/P: True
print(l1 == l3) # O/P: False

# <, > , <=, >= comparisons are also done on values at same position.
# The comparison happens element by element. If first comparison returns True, we stop, else we compare the second elements etc..
# Here 5 > 2 we stop and return True
print(l4 > l3) # O/P: True

# This is true for tuples of unequal lengths.
# Since first two elements match, and `l` has no more elements but `ll` has more elements, `ll` > 'l'
l = (5, 6)
ll = (5, 6, 4)
print(ll > l) # O/P: True
```
# References
[Python Tuples](https://realpython.com/python-tuple/#concatenating-and-repeating-tuples)