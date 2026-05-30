#python 
+ A sequence of values. Values can be of different types
```python
list1 = [ ] # Empty list
# or
list1 = list()

list2 = [1, 2, 3] 
list3 = [1, 'hi', 4.0]
list4 = [[2, 3, 4], [2]] # nested list
```
+ `list()` to copy a list (deep copy). **Don't** name a variable/function `list` , cause the inbuilt function `list()` gets shadowed. 
```python
mylist = [1,2,3]
list2 = list(mylist)
list2[0] = 3
print(mylist, list2)
# [1, 2, 3] [3, 2, 3]
```
+ `len(list)`:  Length of list
+ `t1.append(5)`: Add 5 to end of list
+ `t1.extend(t2)`: Concatenate `t2` at the end of `t1`, returns new list
+ `t1.pop(0)`: Remove by index, modifies the list inplace. Returns popped element
+ `t1.remove(2)` : Remove by value. modifies inplace. Returns `None`. If element not in list, throws `ValueError`.
+ `sorted(t1, reverse=False)` : Sorts list elements in asc order. Returns new list. If `reverse=True`, sorts in desc order.
+ Even if two lists store the same values, they point to different memory locations unless explicitly assigned to the same memory location by **=** operator.
```python
list1 = [2, 3, 4]
list2 = [2, 3, 4] 
print(list1 is list2) # False
list2 = list3 # Explicit assignment
print(list2 is list3) # True
```
+ They are **mutable**
```python
list1 = [2, 3, 4]
list1[3] = 90
list1[4] = 5 # IndexError

print(list1[-1]) # 90 (count backwards)
```
+ **+** operator concatenates lists
```python
t1 = [1, 2]
t2 = [3, 4]
print(t1 + t2) # [1,2,3,4]
```
+ * operator repeats list a given number of times
```python
print(['spam'] * 4) ['spam','spam','spam','spam']
```
+ [[Conditionals#In| in]] operator to check if element is in list
+ `list[3:7]` : splice list from index 3 to 6
	+ Will work even if upper bound given is greater than length of the list. Ex: `list = [1, 2, 3] list[3:70]`.
	+ `list[3:]` implies from index 3 to end
	+ `list[:4]` implies from index 0 to 3
	+ `list[-3]` gets character at third index from end (count from 1).
	+ `list[3:7:2]` implies from index 3 to 6, step=2
	+ `list[::-1]` step = -1 thus returns reverse of list.
+ `sum(t1)` : Sums all elements in the list . 
	+ Doesn't work if list is a mix of numbers and strings. 
	+ `True` = 1 & `False` = 0 for mix of bool and numbers.
	+ Doesn't work for list of only strings
+ `min(t1)`: Min element in list. 
	+ Doesn't work if list is a mix of numbers and strings.
	+ `True` = 1 & `False` = 0 for mix of bool and numbers.
	+ For list of strings, returns lexicographically smaller string
+ `max(t1)`: Max element in list.
	+ Doesn't work if list is a mix of numbers and strings
	+ `True` = 1 & `False` = 0 for mix of bool and numbers.
	+ For list of strings, returns lexicographically smaller string
# List comprehension
```python
numbers = [1, 2, 3, 4, 5]

# create a new list using list comprehension
square_numbers = [num * num for num in numbers]

print(square_numbers)

# Output: [1, 4, 9, 16, 25]
```
## with if
```python
# filtering even numbers from a list
even_numbers = [num for num in range(1, 10) if num % 2 == 0 ]

print(even_numbers)

# Output: [2, 4, 6, 8]
```
## with if-else
```python
numbers = [1, 2, 3, 4, 5, 6]

# find even and odd numbers
even_odd_list = ["Even" if i % 2 == 0 else "Odd" for i in numbers]

print(even_odd_list)
```
## with nested if
```python
# find even numbers that are divisible by 5
num_list = [y for y in range(100) if y % 2 == 0 if y % 5 == 0]

print(num_list)
```