#python 
# Input
+ Standard input, reads in string.
```python
x = input('Prompt here')
```
# Output 
+ Always prints on new line.
```python
name = "David"  
age = 40  
print("Name:", name, "Age:", age)
```
+ To print on same line, `end` [[Functions#Default Arguments|argument]] is passed either a empty string `''` or a space `' '` . By default, it is set to `\n`.
```python
print('Hello', end='')
print('World', end=' ')
print('Hi')
print('Bye')

# HelloWorld Hi
# Bye
```
## Formatting
+ %- formatting (Old)
	+ %s : strings
	+ %d: int
	+ %f: float
	+ %x/%X: int in hex representation (lowercase/uppercase)
	+ %.2f: x digit precision for floats 
```python
# For single arguments:
name = "John"
age = 23
print("Hello, %s!" % name)

## for multiple arguments:
print("%s is %d years old." % (name, age))
```

+ `str.format()` (New style): More flexible
```python
name = "Bob"  
age = 25
print("{} is {} years old.".format(name, age))

pi = 3.14159  
print("Pi with two decimal places: {:.2f}".format(pi))
```

+ f-strings (Preferred): More readable
```python
name = "Charlie"  
age = 35  
print(f"Name: {name}, Age: {age}")

price = 49.99  
print(f"The price is {price:.2f} dollars")
```