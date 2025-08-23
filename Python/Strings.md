#python 
+ Defined using single quotes ==' '==  or double quotes ==" "== .
+ Python. may choose to cache string literal values in a process called ***string interning***.
# Basic String Operations
+ ***CANNOT*** concatenate between numbers and string (like in Java).
```python
one = 1
hello = 'hello'
print(one+hello)
# TypeError: unsupported operand types for +: 'int' and 'str'
```
+ ***CAN*** concatenate strings using **+** operator.
```python
hi = 'hello'
name = 'Mira'
print(hi + ' ' + name) # hello Mira
```
+ Operator _*_ used to repeat a string and concatenate it
```python
print('Spam '*4) # Spam Spam Spam Spam
```
+ `len(a_string)` returns length of string
+ `astring.index('o')` returns first occurrence of `o` .
+ `astring.count('l')` counts no of occurrences of `l`.
+ `astring[3:7]` substring starting from index 3 to 6.
	+ Will work even if upper bound given is greater than length of the string. Ex: `astring='Hello' astring[3:70]`.
	+ `astring[3:]` implies from index 3 to end
	+ `astring[:4]` implies from index 0 to 3
	+ `astring[-3]` gets character at third index from end (count from 1).
	+ `astring[3:7:2]` implies from index 3 to 6, step=2
	+ `astring[::-1]` step = -1 thus returns reverse of string.
+ `astring.upper()` Uppercase of string
+ `astring.lower()` Lowercase of string
+ `astring.startswith("Hi")` True if string starts with `Hi` else False
+ `astring.endswith("Bye")` True if string ends with `Bye` else False
+ `astring.replace('Hi', 'Bye')` Replaces `Hi` with `Bye` .
+ `sorted(astring)` : sorts string characters in ascending order. Returns new list
+ `sorted(astring, reverse=True)` : sorts string characters in descending order. Returns new list
+ Strings are **immutable**. i.e. when changing a string we have to create a new string
```python
greeting = 'Hello World'
new_greeting = 'J' + greeting[1:]
print(new_greeting) # Jello World
```
+ Accessing string using index:  `astring[0]`. If we pass a number > length of string, a `IndexError` is thrown.
+ Comparison using `==` operator for value equality.