#python 
+ Sequence of values, which can be be of any type.  Can be a mix of different types.
+ Indexed by integers.
+ ***IMMUTABLE***
```python
# Assignment
t = 'l', 'u', 'p', 'i', 'n'
# or
t = ('l', 'u', 'p', 'i', 'n')

# single value tuple must include a final comma
t1 = 'p',
# or
t1 = ('p',)

# Empty tuple
t1 = tuple()
# or
t1 = ()
```
+ If an sequence (string, list, tuple) is passed as argument to `tuple()` function, it creates a tuple with the elements of the sequence.
```python
t = tuple('lupin')
print(t) # ('l', 'u', 'p', 'i', 'n')
```
+ Access existing index element using `t[0]` type syntax. IndexError thrown if out of range.
+ Tuples can be sliced like [[List]].
+ **+** operator concatenates tuples
```python
t = tuple('lup') + ('i', 'n')
print(t) # ('l', 'u', 'p', 'i', 'n')
```
+ * operator repeats tuple x times
```python
t = tuple('spam') * 2
print(t) # ('s', 'p', 'a', 'm', 's', 'p', 'a', 'm')
```
+ `sorted()` to sort tuple, but it returns a list. The default behavior is to use the first element from each tuple to sort the list, and use the second element to break ties.
+ Since they are immutable, if we try to update a value, `TypeError` is thrown.
+ Since they are immutable, no function to remove values.
+ Tuple unpacking
``` python
t1 = (1, 2)

# Unpacking
a, b = t1 # a=1, b=2

# ValueError is thrown if R.H.S != L.H.S
a, b = (1, 2, 3)

# *args: * operator unpacks tuple of arguments
def mean(*args):
    return sum(args) / len(args)

mean(1, 2, 3)
```
+ Iteration same as [[List]].
+ Comparing tuples: compares the first element from each sequence. If they are equal, it goes on to the next pair of elements, and so on, until it finds a pair that differ.
```python
print((0, 1, 2) < (0, 3, 4)) # True
```