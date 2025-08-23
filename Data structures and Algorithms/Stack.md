```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
#dsa 
# Space and Time Complexity
| ****Operations**** | ****Time Complexity**** | ****Space Complexity**** |
| ------------------ | ----------------------- | ------------------------ |
| push()             | O(1)                    | O(1)                     |
| pop()              | O(1)                    | O(1)                     |
| top() or peek()    | O(1)                    | O(1)                     |
| isEmpty()          | O(1)                    | O(1)                     |
| isFull()           | O(1)                    | O(1)                     |
# Array Implementation

## Structure
```java
class Stack {
	private int[] nums;
	private int size;
	private int top;

	public Stack(int initialSize) {
		this.size = 0;
		this.nums = new int[initialSize];
		this.top = -1;
	}
}
```

## Pros and Cons:
### Pros
- Simple and efficient for stacks with a known maximum size.
- Fast access to elements due to direct indexing.
### Cons
- Fixed size, which can lead to stack overflow if the number of elements exceeds the array size.
- Inefficient for stacks that require frequent resizing.
# Linked List Implementation

## Structure
```java
class Stack {
	private Node top;
	private int size;

	public Stack() {
		this.top = null;
		this.size = 0;
	}
}
```

## Pros and Cons

### Pros
- Dynamic size, which means it can grow or shrink as needed.
- Efficient for stacks with frequent push and pop operations.
### Cons
- Requires more memory due to the overhead of storing node pointers.
- Slower access to elements compared to arrays.