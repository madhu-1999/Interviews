```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Bubble Sort
+ **Bubble sort** is [a sorting algorithm](https://www.programiz.com/dsa/sorting-algorithm) that compares two adjacent elements and swaps them until they are in the intended order.
+ After each iteration, the largest element among the unsorted elements is placed at the end.
```
bubbleSort(array)
  for i <- 1 to sizeOfArray - 1
    for j <- 1 to sizeOfArray - 1 - i
      if leftElement > rightElement
        swap leftElement and rightElement
end bubbleSort
```

|                      |          |
| -------------------- | -------- |
| **Time Complexity**  |          |
| Best                 | $O(n)$   |
| Worst                | $O(n^2$) |
| Average              | $O(n^2)$ |
| **Space Complexity** | $O(1)$   |
| **Stability**        | Yes      |
# Selection Sort
+ Selection sort is [a sorting algorithm](https://www.programiz.com/dsa/sorting-algorithm) that selects the smallest element from an unsorted list in each iteration and places that element at the beginning of the unsorted list.
```
selectionSort(array, size)
  for i from 0 to size - 1 do
    set i as the index of the current minimum
    for j from i + 1 to size - 1 do
      if array[j] < array[current minimum]
        set j as the new current minimum index
    if current minimum is not i
      swap array[i] with array[current minimum]
end selectionSort
```

|                      |          |
| -------------------- | -------- |
| **Time Complexity**  |          |
| Best                 | $O(n^2)$ |
| Worst                | $O(n^2)$ |
| Average              | $O(n^2)$ |
| **Space Complexity** | $O(1)$   |
| **Stability**        | No       |
# Insertion Sort
+ Insertion sort is [a sorting algorithm](https://www.programiz.com/dsa/sorting-algorithm) that places an unsorted element at its suitable place in each iteration.
+ Insertion sort works similarly as we sort cards in our hand in a card game. We assume that the first card is already sorted then, we select an unsorted card. If the unsorted card is greater than the card in hand, it is placed on the right otherwise, to the left. In the same way, other unsorted cards are taken and put in their right place.
```
insertionSort(array)
  mark first element as sorted
  for each unsorted element X
    'extract' the element X
    for j <- lastSortedIndex down to 0
      if current element j > X
        move sorted element to the right by 1
    break loop and insert X here
end insertionSort
```

|                      |          |
| -------------------- | -------- |
| **Time Complexity**  |          |
| Best                 | $O(n)$   |
| Worst                | $O(n^2)$ |
| Average              | $O(n^2)$ |
| **Space Complexity** | $O(1)$   |
| **Stability**        | Yes      |
# Merge sort
+ A problem is divided into multiple sub-problems. Each sub-problem is solved individually. Finally, sub-problems are combined to form the final solution.
```
MergeSort(A, p, r):
    if p > r 
        return
    q = (p+r)/2
    mergeSort(A, p, q)
    mergeSort(A, q+1, r)
    merge(A, p, q, r)
```

|                      |            |
| -------------------- | ---------- |
| **Time Complexity**  |            |
| Best                 | O(n*log n) |
| Worst                | O(n*log n) |
| Average              | O(n*log n) |
| **Space Complexity** | O(n)       |
| **Stability**        | Yes        |
# Quicksort
+  An array is divided into subarrays by selecting a **pivot element** (element selected from the array). 
+ While dividing the array, the pivot element should be positioned in such a way that elements less than pivot are kept on the left side and elements greater than pivot are on the right side of the pivot.
+  The left and right subarrays are also divided using the same approach. This process continues until each subarray contains a single element.
+  At this point, elements are already sorted. Finally, elements are combined to form a sorted array.
```
quickSort(array, leftmostIndex, rightmostIndex)
  if (leftmostIndex < rightmostIndex)
    pivotIndex <- partition(array,leftmostIndex, rightmostIndex)
    quickSort(array, leftmostIndex, pivotIndex - 1)
    quickSort(array, pivotIndex, rightmostIndex)

partition(array, leftmostIndex, rightmostIndex)
  set rightmostIndex as pivotIndex
  storeIndex <- leftmostIndex - 1
  for i <- leftmostIndex + 1 to rightmostIndex
  if element[i] < pivotElement
    swap element[i] and element[storeIndex]
    storeIndex++
  swap pivotElement and element[storeIndex+1]
return storeIndex + 1
```

|                      |            |
| -------------------- | ---------- |
| **Time Complexity**  |            |
| Best                 | O(n*log n) |
| Worst                | O(n2)      |
| Average              | O(n*log n) |
| **Space Complexity** | O(log n)   |
| **Stability**        | No         |