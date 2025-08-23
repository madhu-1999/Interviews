#dsa 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
+ FIFO order.  Two pointers front and rear. Initially front=rear=-1
+ Enqueue from rear. deque from front
# Time and Space Complexity

| Operation          | Time Complexity | Space Complexity |
| ------------------ | --------------- | ---------------- |
| Enqueue            | O(1)            | O(1)             |
| Deque              | O(1)            | O(1)             |
| Peek               | O(1)            | O(1)             |
| isEmpty()          | O(1)            | O(1)             |
| isFull()           | O(1)            | O(1)             |
|                    |                 |                  |
# Circular Queue
## Array Implementation
+ Two pointers front=rear=-1
+ Enqueue next position: (rear+1) % length
+ Queue full: (rear+1) % length == front
+ Queue empty: front=-1
+ Deque next position = (front+1) % length
## Linked List Implementation
+ rear=null $\rightarrow$ queue empty check
+ Circular ll used to implement
+ front = rear.next