#graph #dsa 
# Reference
https://www.youtube.com/watch?v=eTaWFhPXPz4
# Disjoint set 
+ Two sets are disjoint if they have no element in common.
+ It supports following operations:
	+ Merging two disjoint sets to a single set ([[#Union]])
	+ Finding representative of a disjoint set ([[#Find]])
	+ ==Check if two elements belong to same set or not.==
		+ Find representative of both and check if same.
## Representative
Assume A = {0, 1, 3, 4} and B = {2, 5}
__0__ is representative of set A and __5__ is representative of set B.
Both are stored are ==trees== with __0__ and __5__ as root of tree.
![[Screenshot 2025-09-25 at 4.04.27 PM.png]]
## Parent
+ Array of all elements where parent[i] = parent of i<sup>th</sup> element. 
	+ Ex: From above diagram: parent of 4 is 3.
+ Initially, `parent[i] = i` for all elements.
	+ Then we merge ([[#Union]]) as relationships between elements are processed
# Find
+ Find representative of given element (root of tree) by recursively traversing parent array until we reach parent ( `parent[i] = i` )
```java
public int find(int i) {
      
        // if i itself is root or representative
        if (parent[i] == i) {
            return i;
        }
      
        // Else recursively find the representative
        // of the parent 
        return find(parent[i]);
    }
```
+ Parent array for above diagram is:
![[Pasted image 20250925162209.png]]
# Union
+ Takes two elements, finds their representatives using [[#Find]] and makes one of the trees a child of the other i.e. representative of tree B (__5__) becomes a child of representative of tree A (__0__).
```java
public void union(int i, int j) {
      
        // Representative of set containing i
        int irep = find(i);

        // Representative of set containing j
        int jrep = find(j);

        // Make the representative of i's set be 
        // the representative of j's set
        parent[irep] = jrep;
    }
```
![[Screenshot 2025-09-25 at 4.25.34 PM 1.png]]
# Time Complexity
O(n)
# Optimized Union-Find
+ Main idea is to reduce heights of trees representing different sets.
## Find (Path compression)
+ Instead of storing parent of a element in its tree, parent is the root (representative) of tree.
+ Parent array now becomes:
![[Pasted image 20250925163137.png]]
Note that parent[4] = 0 now instead of 3
+ Representative trees also flatten:
![[Pasted image 20250925163902.png]]
```java
public int find(int i) {
        
        int root = parent[i];
      
        if (parent[root] != root) {
            return parent[i] = find(root);
        }
      
        return root;
    }
```
## Union By Rank
+ Rank is height of trees (no path compression).
	+ With path compression, rank[i] >= height of tree. (Remember, we try to keep height as low as possible)
+ Used to determine which tree becomes the child. (one with smaller rank becomes child)
+ Rank array (no path compression):
![[Pasted image 20250925165218.png]]
With path compression(refer path-compressed trees):
![[Pasted image 20250925165335.png]]
```java
void union(int x, int y)
    {
        // Find representatives of two sets
        int xRoot = find(x), yRoot = find(y);

        // Elements are in the same set, no need
        // to unite anything.
        if (xRoot == yRoot)
            return;

        // If x's rank is less than y's rank
        if (rank[xRoot] < rank[yRoot])

            // Then move x under y  so that depth
            // of tree remains less
            parent[xRoot] = yRoot;

        // Else if y's rank is less than x's rank
        else if (rank[yRoot] < rank[xRoot])

            // Then move y under x so that depth of
            // tree remains less
            parent[yRoot] = xRoot;

        else // if ranks are the same
        {
            // Then move y under x (doesn't matter
            // which one goes where)
            parent[yRoot] = xRoot;

            // And increment the result tree's
            // rank by 1
            rank[xRoot] = rank[xRoot] + 1;
        }
    }
```
For `union(1,2)` , since rank[0] > rank[5] tree B becomes child of tree A.
![[Pasted image 20250925165838.png]]
## Time complexity
Worst Case: O(n)
Amortized: O($\alpha(n)$) where $\alpha$ = Inverse Ackermann function