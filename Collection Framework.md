#java 
![Mastering the Java Collections Framework Hierarchy With Java Code and JUnit  Testing | by Mahad | Medium](https://miro.medium.com/v2/resize:fit:822/1*qgcrVwo8qzF4muOQ-kKB8A.jpeg)
# What is Framework?
Chunk of code (classes, interfaces) written on top of a language's core libraries to solve common problems.

# Iterable Interface
+ Present in `java.lang.iterable` package.
+ Allows iteration sequentially across a collection object from front to end.
+ Collection framework extends Iterable thus **all classes implementing collection framework can access Iterable methods**.
+ 3 methods of acces:
	+ Enhanced for loop `for(int num : arr)`
	+ `forEach()` method `arr.forEach(x->x+1);`
	+ `iterator()` method. This methods calls the the **Iterator** interface which returns an iterator that can iterate over an object of Collection framework class.
	```java
Iterator<Integer> iter;
        iter = stack.iterator();
        // iterating elements of the set
        while (iter.hasNext()) {
            int nextElement = iter.next();
            System.out.print(nextElement + " ");
        }
```

# List Interface
+ Ordered collection of elements. 
+ Maintained the order of elements in which they are added.
- Allows the duplicate elements.
- Allows nulls.
- The List interface offers methods to access elements by their index and includes the ***listIterator() method***, which returns a ***ListIterator***. Can iterate front to back and in reverse.
- Operations
	- add(Object o)
	- add(int index, Object o) :  If we try to add element at index 1 before adding elements at index 0 it will throw an error. It is always recommended to add elements in a particular index only when the size is defined or to add them sequentially.
	- set(int index, Object o) : Updating elements
	- indexOf(Object o):Index of **first** occurrence or -1
	- lastIndexOf(Object o): Index of **last** occurrence or -1
	- remove(Object o)
	- remove(int index)
	- get(int index)
	- contains(Object o)
	- containsAll(Collection collection): Checks if the list contains all the collection of elements
	- addAll(Collection collection): Adds all elements from collection at the end of the list
	- addAll(int index, Collection collection):Adds all elements from collection starting from ***index***
	- sort(Comparator comp): sort on basis of ***comp***.
	- equals(Object o) : Two lists are equal if they have same size and all elements are present in the lists in the **same order**.
	- isEmpty()
+ ![AbstractSequentialList-in-Java](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20201111232610/AbstractSequentialList-in-Java1.png)