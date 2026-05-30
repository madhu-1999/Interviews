#python 
+ Think HashMap in Java
+ Dictionary uses a hash table underneath. So, keys can only be of **immutable** types i.e. `int`, `float`, `str`, `tuple`.
```python
phonebook = {} # Empty dictionary
phonebook["John"] = 938477566 # Can be used to add to dict
phonebook["Jack"] = 938377264
phonebook["Jill"] = 947662781
print(phonebook) 
# {'Jack': 938377264, 'John': 938477566, 'Jill': 947662781}

# Another way to assign
phonebook = {
    "John" : 938477566,
    "Jack" : 938377264,
    "Jill" : 947662781
}
```
+ `dict()` to make deep copy of dictionary.
+ `in` operator to check if a key/value exists in dict
```python
phonebook = {
    "John" : 938477566,
    "Jack" : 938377264,
    "Jill" : 947662781
}

print('John' in phonebook) # True
print(9823784673 in phonebook.values()) # False
```
+ Iteration
```python
# Loop over key and value
for key, value in phonebook.items():
	pass

# Loop over keys
for key in phonebook:
	pass

# Loop over values
for value in phonebook.values():
	pass
```
+ `pop(key)` : Remove from dict by key.
+ `del phonebook['John']` : Remove from dict by key
+ They are **mutable**.