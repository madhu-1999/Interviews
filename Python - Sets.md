---
tags:
  - "#python"
  - "#dsa"
---
# Overview
+ [i] __Mutable__
+ [i] __Unordered__
+ [i] [Iterable](Python%20-%20Iterable%20and%20Iterators.md)  
+ [i] They can store objects of different data types.
	+ [!] The objects __MUST__ be hashable i.e. we __CANNOT__ store mutable types like [lists](Python%20-%20List.md), [dictionaries](Python%20-%20Dictionary.md) or sets!
+ [*] They only store __unique__ elements.
# Creating sets
## Using literals
```python
s1 = {'#F1C40F', '#33FF57', '#E74C3C', '#3357FF'}
```
## Using `set()`
```python
s1 = set()

s2 = set([1, 2, 3, 4, 5]) # Adds each element in list to set

s3 = set('python') # {'p', 'y', 't', 'h', 'o', 'n'}
```
## Using [set comprehensions](Python%20-%20Comprehensions)
```python
usernames = ['Alice', 'Bob', 'charlie', 'Charlie', 'bob']
# {'bob', 'alice', 'charlie'}
s1 = {name.lower().strip() for name in usernames}
```
# Union
## Using `|` operator
+ [!] All operands must be sets.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# Union using `|` operator
# O/P: {'hamster', 'chicken', 'dog', 'cat', 'goat', 'cow', 'parrot'}
print(pet_animals | farm_animals)

# This throws a TypeError
print(pet_animals | ['cow', 'chicken'])
```
## Using `union()` 
+ [i] All operands must be iterable.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# Union using `union()` function
# O/P: {'hamster', 'chicken', 'dog', 'cat', 'goat', 'cow', 'parrot'}
print(pet_animals.union(farm_animals))

# O/P: {'hamster', 'chicken', 'dog', 'cat', 'goat', 'cow', 'parrot'}
print(pet_animals.union(["cow", "chicken", "goat", "dog", "cat"]))
```
# Intersection
## Using `&` operator
+ [!] All operands must be sets.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'cat', 'dog'}
print(pet_animals & farm_animals)

# This throws a TypeError
print(pet_animals & ['cow', 'chicken'])
```
## Using `intersection()` 
+ [i] All operands must be iterable.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'cat', 'dog'}
print(pet_animals.intersection(farm_animals))

# O/P: set()
print(pet_animals.intersection(['cow', 'chicken']))
```
# Difference
## Using `-` operator
+ [!] All operands must be sets.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'hamster', 'parrot'}
print(pet_animals - farm_animals)

# This will throw a TypeError
print(pet_animals - ['cow', 'chicken'])
```
## Using `difference()`
+ [i] All operands must be iterable.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'hamster', 'parrot'}
print(pet_animals.difference(farm_animals))

# O/P: {'hamster', 'parrot', 'dog', 'cat'}
print(pet_animals.difference(['cow', 'chicken']))
```
# Symmetric Difference
The **symmetric difference** between two sets is a new set containing all the elements that appear in either set but not both.
## Using `^` operator
+ [!] All operands must be sets.
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'goat', 'parrot', 'cow', 'hamster', 'chicken'}
print(pet_animals ^ farm_animals)

# This will throw a TypeError
print(pet_animals ^ ['cow', 'chicken'])
```
## Using `symmetric_difference()`
+ [i] __Both__ operands must be iterable
```run-python
pet_animals = {"dog", "cat", "hamster", "parrot"}
farm_animals = {"cow", "chicken", "goat", "dog", "cat"}

# O/P: {'goat', 'parrot', 'cow', 'hamster', 'chicken'}
print(pet_animals.symmetric_difference(farm_animals))

# O/P: {'cat', 'chicken', 'hamster', 'parrot', 'dog', 'cow'}
print(pet_animals.symmetric_difference(['cow', 'chicken']))

# Only works for two operands!
# This throws a TypeError
print(pet_animals.symmetric_difference(farm_animals, wild_animals))
```
# Comparing Sets
## Subset check
```run-python
required_ingredients = {"cheese", "eggs", "milk"}
available_ingredients = {"cheese", "eggs", "milk", "sugar", "salt"}

# O/P: True
print(required_ingredients <= available_ingredients)
# O/P: True
print(required_ingredients.issubset(available_ingredients))

# Proper subset
# O/P: True
print(required_ingredients < available_ingredients)
```
## Superset check
```run-python
required_ingredients = {"cheese", "eggs", "milk"}
available_ingredients = {"cheese", "eggs", "milk", "sugar", "salt"}

# O/P: True
print(available_ingredients >= required_ingredients)
# O/P: True
print(available_ingredients.issuperset(required_ingredients))

# Proper superset
# O/P: True
print(available_ingredients > required_ingredients)
```
## Disjoint sets
+ [I] We could use [#Intersection](#Intersection) operators, but remember, disjoint sets will return a empty set, not a boolean!
	+ [i] Empty sets are _falsy_ values, so it would work the same in a condition. 
```run-python
required_ingredients = {"cheese", "eggs", "milk"}
available_ingredients = {"sugar", "salt"}

# O/P: True
print(available_ingredients.isdisjoint(required_ingredients))
```
## Equality
If two sets have the same elements, they are equal.
```run-python
s1 = {"cheese", "eggs", "milk"}
s2 = {"cheese", "eggs", "milk"}

# O/P: False
print(id(s1) == id(s2))
# O/P: True
print(s1 == s2)
```
# Adding elements
```run-python
employees = {"Alice", "Charlie"}

# Adding a single element
employees.add("John")
print(employees) # O/P: {'Charlie', 'Alice', 'John'}

# add() only takes 1 argument
# This will throw a TypeError
employees.add("Jane", "Bob")
```
# Removing elements
## Using `remove()`

```run-python
employees = {"Alice", "Charlie", "John", "Laura"}

# Removing single element
employees.remove("Charlie")
print(employees) # O/P: {'Laura', 'John', 'Alice'}

# Removing an element that doesn't exist raises a KeyError
employees.remove("Linda")
```
## Using `discard()`

```run-python
employees = {"Alice", "Charlie", "John", "Laura"}

# Removing single element
employees.discard("Charlie")
print(employees) # O/P: {'Laura', 'John', 'Alice'}

# Removing an element that doesn't exist does nothing
employees.discard("Linda")
print(employees)# O/P: {'Laura', 'John', 'Alice'}
```
## Using `pop()`

```run-python
employees = {"Alice", "Charlie", "John", "Laura"}

# Removes and returns a random element
removed = employees.pop()
print(employees) # {'Alice', 'Laura', 'Charlie'}

# If set is empty, it raises a KeyError
employees = {}
employees.pop()
```
## Using `clear()`

```run-python
employees = {"Alice", "Charlie", "John", "Laura"}

# Removes all elements
removed = employees.clear()
print(employees) # set()
```
# Sorting a set
+ [!]  Only __homogenous__ sets can be sorted!
+ [!] `sort()` __CANNOT__ be used, as it mutates the object. Since sets are unordered, the function will not work.
+ [!] `sorted()` returns a new list, we must convert it back into a set.

```run-python
numbers = {2, 9, 5, 1, 6}

# It returns a new list of the sorted numbers in ascending order
print(sorted(numbers)) # O/P: [1, 2, 5, 6, 9]
print(numbers) # O/P: {2, 9, 5, 1, 6}

# This returns a new sorted list in reverse order
print(sorted(numbers, reverse=True)) # O/P: [9, 6, 5, 2, 1]
```
## Custom sorting
We can do custom sorting, using the `key` parameter which takes a lambda function as an argument. 
+ [!] Lambda functions in Python __must__ be a one-liner. 
```run-python
# Sample data: (Name, Grade, Age)
students = {("Alice", "B", 12), ("Bob", "A", 15), ("Charlie", "B", 10)}

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
>Remember, sets only stores hashable objects like tuples, str, int, float etc.. 
>Sorting on multiple fields depends on the particular object being used.
# References
[Sets in Python](https://realpython.com/python-sets/)

