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
# Singly Linked List
## Space & Time Complexity

| Operation          | Time Complexity | Space Complexity |
| ------------------ | --------------- | ---------------- |
| Insert at Start    | O(1)            | O(1)             |
| Insert at Position | O(n)            | O(1)             |
| Insert at End      | O(1)            | O(1)             |
| Delete at Start    | O(1)            | O(1)             |
| Delete at End      | O(n)            | O(1)             |
| Delete at Position | O(n)            | O(1)             |
| Linear Search      | O(n)            | O(1)             |
| Access             | O(n)            | O(1)             |
|                    |                 |                  |

## Java

### **Structure**
```java
 class Node {
	int data;
	Node next;

	public Node(int data){
		this.data = data;
	}
}

class LinkedList {
	Node head;

	public LinkedList(Node head) {
		this.head = head;
	}
}
```

### **Insert at end**
```java
void insertEnd(int data) {
	if(head == null) {
		head = new Node(data);
		return;
	}
	
	Node curr = this.head;
	while(curr.next != null) {
		curr = curr.next;
	}
	curr.next = new Node(data);
}
```

### **Insert at start**
```java
void insertStart(int data){
	Node node = new Node(data);
	node.next. = head;
	head = node;
}
```

### **Insert at position**
```java
void insertAfter(int addAfter, int data) {
	Node node = new Node(data);
	Node curr = head;
	while(curr != null) {
		if(curr.data == addAfter){
			node.next = curr.next;
			curr.next = node;
			break;
		}
		curr = curr.next;
	}
}
```

### **Delete at end**
```java
Node deleteEnd() {
	if(head == null || head.next == null) {
		head = null;
		return;
	}

	Node curr = head, prev = null;
	while(curr.next != null) {
		prev = curr;
		curr = curr.next;
	}
	prev.next = null;
	return curr;
}
```

### **Delete from start**
```java
Node deleteStart() {
	Node curr = head;
	if(head == null || head.next == null) {
		head = null;
		return curr;
	}
	head = head.next;
	curr.next = null;
	return curr;
}
```

# Doubly Linked List

## Space and Time Complexity
|                            |                                 |                      |
| -------------------------- | ------------------------------- | -------------------- |
| **Operation**              | **Time Complexity: Worst Case** | **Space Complexity** |
| Insert at beginning or end | O(1)                            | O(1)                 |
| Delete at beginning or end | O(1)                            | O(1)                 |
| Search                     | O(n)                            | O(1)                 |
| Access                     | O(n)                            | O(1)                 |
## Structure
```java
class Node {
	int data;
	Node prev;
	Node next;

	public Node(int data) {
		this.data = data;
	}
}

class DoublyLinkedList {
	Node head;
}
```

## Java

### **Insert at end** 
```java
void addEnd(int data) {
	if(head == null){
		head = new Node(data);
		return;
	}
	Node node = new Node(data);
	Node curr = head;
	while(curr.next != null) {
		curr = curr.next;
	}
	curr.next = node;
	node.prev = curr;
}
```

### **Insert at start**
```java
void addStart(int data) {
	Node node = new Node(data);
	if(head == null){
		head = node;
		return;
	}
	node.next = head;
	head.prev = node;
	head = node;
}
```

### **Insert at position**
```java
void addAfter(int addAfter, int data) {
	Node node = new Node(data);
	Node curr = head;
	while(curr != null) {
		if(curr.data == addAfter) {
			node.next = curr.next;
			node.prev = curr;
			if(curr.next != null)
				curr.next.prev = node;
			curr.next = node;
			break;
		}
		curr = curr.next;
	}
}
```

### **Circular linked list**
Can be both singly linked or doubled linked. The last node points to the first, so there is no *head* variable, we track *last* node.

## Structure
```java
// Assume singly linked list
class CircularLinkedList{
	Node last;
}
```

## Space and Time Complexity

| **Operation**              | **Time Complexity: Worst Case** | **Space complexity** |
| -------------------------- | ------------------------------- | -------------------- |
| Insert at beginning or end | O(1)                            | O(1)                 |
| Delete at beginning        | O(1)                            | O(1)                 |
| Search                     | O(n)                            | O(1)                 |
| Access                     | O(n)                            | O(1)                 |
| Insert After a node        | O(n)                            | O(1)                 |
| Delete at end or after     | O(n)                            | O(1)                 |
## Java

### **Insert at end**
```java
Node insertEnd(Node tail, int value){
        Node newNode = new Node(value);
        if (tail == null) {
            tail = newNode;
            newNode.next = newNode;
        }
        else {
            newNode.next = tail.next;
            tail.next = newNode;
            tail = newNode;
        }
        return tail;
    }
```

### **Insert at start
```java
public Node insertAtBeginning(Node last,
                                         int value){
        Node newNode = new Node(value);
        if (last == null) {
            newNode.next = newNode;
            return newNode;
        }

        newNode.next = last.next;
        last.next = newNode;

        return last;
    }
```

### **Delete start**
```java
public Node deleteFirstNode(Node last) {
        if (last == null) {
            // If the list is empty
            System.out.println("List is empty");
            return null;
        }

        Node head = last.next;

        if (head == last) {
            // If there is only one node in the list
            last = null;
        } else {
            // More than one node in the list
            last.next = head.next;
        }

        return last;
    }
```

### **Delete end**
```java
public Node deleteLastNode(Node last){
        if (last == null) {
            // If the list is empty
            System.out.println(
                "List is empty, nothing to delete.");
            return null;
        }
        Node head = last.next;

        // If there is only one node in the list
        if (head == last) {
            last = null;
            return last;
        }
        // Traverse the list to find the second last node
        Node curr = head;
        while (curr.next != last) {
            curr = curr.next;
        }
        // Update the second last node's next pointer to
        // point to head
        curr.next = head;
        last = curr;

        return last;
    }
```