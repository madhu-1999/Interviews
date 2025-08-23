#python 
# For 
+ Iterate over a sequence
```python
for i in range(2):
	print(i)
# 0
# 1

for i in range(1,3):
	print(i)

# 1
# 2

for i in range(1,5,2): # step=2
	print(i)

# 1
# 3
```
+ Iterate over a iterable like [[List]] 
```python
for name in ['John', 'Rick']
	print(name)

# John
# Rick
```
# While
+ Loop until condition is true
```python
count = 2
while(count > 0):
	print(count)
	count-=1
# 2
# 1
```
# Else clause in loops
+ When the loop condition of "for" or "while" statement fails then code part in "else" is executed. 
+ If a **break** statement is executed inside the for loop then the "else" part is skipped.
+ The "else" part is executed even if there is a **continue** statement.