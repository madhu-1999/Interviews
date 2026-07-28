---
tags:
  - "#python"
  - "#dsa"
---
# Overview
+ [i] Analogous to Java LinkedHashMap
+ [i] __Mutable__
+ [i] __Ordered__ by insertion order
+ [i]  They can store objects of different types as __values__.
+ [i] __Keys__ must be:
	+ Hashable
	+ Unique (overwritten, if inserted again)
# Creating dictionary
## Using literals
```python
# Empty dict
d1 = {}

mlb_teams = 
{
    'Colorado': 'Rockies',
    'Chicago': 'Cubs',
    'Boston': 'Red Sox',
    'Minnesota': 'Twins',
    'Milwaukee': 'Brewers',
    'Seattle': 'Mariners',
}
```
## Using `dict()`

```python
# Empty dict
d1 = dict()

# Passing in keyword arguments
d2 = dict(
    Colorado= 'Rockies',
    Chicago= 'Cubs',
)

# Passing in an iterable
d3 = dict([
    ('Colorado', 'Rockies'),
    ('Chicago', 'Cubs')
])

# Using two iterables and `zip`
places = ['Colorado', 'Chicago']
teams = ['Rockies', 'Cubs']

d4 = dict(zip(places, teams))
```
## Using dictionary [comprehensions](Python%20-%20Comprehensions)
```run-python
squares = {integer: integer**2 for integer in range(1, 10)}
print(squares)
# O/P: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}
```
# Accessing elements
```run-python
mlb_teams = {
    'Colorado': 'Rockies',
    'Chicago': 'Cubs',
    'Boston': 'Red Sox',
    'Minnesota': 'Twins',
    'Milwaukee': 'Brewers',
    'Seattle': 'Mariners',
}

# Accessing a value from its key
print(mlb_teams['Colorado']) # O/P: Rockies
print(mlb_teams.get('Colorado')) # O/P: Rockies

# Accessing a key that does not exist
print(mlb_teams.get('New York')) # O/P: None
# Returns the default value, if provided.
print(mlb_teams.get('New York', 'Unknown')) # O/P: Unknown
print(mlb_teams['New York']) # This throws a KeyError
```
## Fetching keys/values/key-value pairs
```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

# Getting all keys
print(inventory.keys()) # O/P: dict_keys(['apple', 'orange', 'banana'])

# Getting all values
print(inventory.values()) # O/P: dict_values([100, 80, 100])

# Getting all key-value pairs 
print(inventory.items()) # O/P: dict_items([('apple', 100), ('orange', 80), ('banana', 100)])
```
# Upserting key-value pairs
If key exists, its value is overwritten, else new key-value pair is created.

```run-python
person = {}
person["first_name"] = "John"
person["children"] = ["Ralph", "Betty", "Bob"]
person["pets"] = {"dog": "Frieda", "cat": "Sox"}
print(person)
''' O/P:
{
    'first_name': 'John', 
    'children': ['Ralph', 'Betty', 'Bob'], 
    'pets': {'dog': 'Frieda', 'cat': 'Sox'}
}
'''
```
## Merging two dictionaries
### Using `update()`
We can merge dictionaries with the following rules:
1. If key is not in first dict, but in second dict, it gets added to first dict.
2. If key is in both dicts, the value in first dict is overwritten by the value in second dict.

```run-python
config = {
     "color": "green",
     "width": 42,
     "height": 100,
     "font": "Courier",
}

user_config = {
     "path": "/home",
     "color": "red",
     "font": "Arial",
     "position": (200, 100),
}

config.update(user_config)
print(config)
''' O/P:
{
    'color': 'red', 
    'width': 42, 
    'height': 100, 
    'font': 'Arial', 
    'path': '/home', 
    'position': (200, 100)
}
'''
```
## Using `|` operator
```run-python
config = {
     "color": "green",
     "width": 42,
     "height": 100,
     "font": "Courier",
}

user_config = {
     "path": "/home",
     "color": "red",
     "font": "Arial",
     "position": (200, 100),
}

config |= user_config
print(config)
''' O/P:
{
    'color': 'red', 
    'width': 42, 
    'height': 100, 
    'font': 'Arial', 
    'path': '/home', 
    'position': (200, 100)
}
'''
```
# Removing key-value pairs
## Using `pop()`

```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

# If key exists, it removes and returns value. The key is also removed.
print(inventory.pop("apple")) # O/P: 100
print(inventory) # O/P; {'orange': 80, 'banana': 100}

# If key does not exist, a KeyError is raised
print(inventory.pop("apple"))
```
## Using `del` statement

```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

del inventory['apple']
print(inventory) # O/P: {'orange': 80, 'banana': 100}

# If key does not exist, it raises KeyError
del inventory['apple']
```
## Using `popitem()`

```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

# Removes and returns a key-value pair as a tuple. Pairs removed in LIFO order
print(inventory.popitem()) # O/P: ('banana', 100)
print(inventory) # O/P; {'apple': 100, 'orange': 80}

#If the dictionary is empty, then it raises a KeyError
inventory = {}
inventory.popitem()
```
## Using `clear()`

```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

# Removes all key-value pairs
inventory.clear()
print(inventory) # O/P: {}
```
# Comparing dictionaries
Two dictionaries are equal if they have the same key-value pairs, even if they are not in the same order.

```run-python
print({1: 1, 2: 2, 3: 3} == {3: 3, 2: 2, 1: 1}) # O/P : True
```
## Sorting
+ [!] Keys must be __homogenous__
+ [!] Cannot be used with `sort()`.
+ [!] `sorted()` returns a list, so we must explicitly convert it back into a dict. 
```run-python
inventory = {"apple": 100, "orange": 80, "banana": 100}

# Sort dictionary by keys
# O/P: {'orange': 80, 'apple': 100, 'banana': 100}
print(dict(sorted(inventory.items(), key=lambda item: item[0])))
# O/P: ['apple', 'banana', 'orange']
print(sorted(inventory.keys()))

# sort by values descending
# O/P: {'apple': 100, 'banana': 100, 'orange': 80}
print(dict(sorted(inventory.items(), key=lambda item: item[1], reverse=True)))
# O/P: [100, 100, 80]
print(sorted(inventory.values(), reverse = True))
```
# dict like classes
# References
[Python dictionary](https://realpython.com/python-dicts/)