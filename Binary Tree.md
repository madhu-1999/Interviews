#dsa 
# Types of Trees
![Types of Trees](https://youtu.be/_ANrF3FJm7I?list=PLgUwDviBIf0oF6QL8m22w1hIDC1vJ_BHz) 
## Full Binary Tree
Every node has either **0 or 2** children.
## Complete Binary Tree
1. All levels are completed filled i.e. 2 children except the last level which may or may not be completely filled.
2. The last level must be filled from left to right.
## Perfect Binary Tree
All the leaf nodes are at the same level.
## Balanced Binary Tree
Height of tree is at most $log(N)$ where $N$= number of nodes in the tree.
# Binary Tree Representation
```java
class Node {
	int data
	Node left;
	Node right;
}
```
# Tree Traversals
## Inorder Traversal
>[!tldr] Left -> Root -> Right

```media-slider
![Pasted image 20260316185754.png](Pasted%20image%2020260316185754.png)
![Pasted image 20260316185804.png](Pasted%20image%2020260316185804.png)
![Pasted image 20260316185813.png](Pasted%20image%2020260316185813.png)
![Pasted image 20260316185902.png](Pasted%20image%2020260316185902.png)
![Pasted image 20260316185838.png](Pasted%20image%2020260316185838.png)
![Pasted image 20260316185917.png](Pasted%20image%2020260316185917.png)
```
````tabs
tab: Recursive
```
```java
public class Tree {
	static void inOrder(Node node, List<Integer> res) {
        if (node == null)
            return;
        
        // Traverse the left subtree first
        inOrder(node.left, res);
        // Visit the current node
        res.add(node.data);
        // Traverse the right subtree last
        inOrder(node.right, res);
    }
}
```
tab: Iterative
```java
public class Tree {
	static ArrayList<Integer> inOrder(Node root) {
        ArrayList<Integer> ans = new ArrayList<>();
        Stack<Node> s = new Stack<>();
        Node curr = root;

        while (curr != null || !s.isEmpty()) {

            // Reach the left most Node of the curr Node
            while (curr != null) {

                // Place pointer to a tree node on
                // the stack before traversing
                // the node's left subtree
                s.push(curr);
                curr = curr.left;
            }

            // Current must be NULL at this point
            curr = s.pop();
            ans.add(curr.data);

            // we have visited the node and its
            // left subtree. Now, it's right
            // subtree's turn
            curr = curr.right;
        }

        return ans;
    }
}
```
````
***Time Complexity:*** O(n)
***Auxiliary Space:*** O(h), h is the height of the tree
## PreOrder Traversal
>[!tldr] Root -> Left -> Right

```media-slider
![Pasted image 20260316190530.png](Pasted%20image%2020260316190530.png)
![Pasted image 20260316190631.png](Pasted%20image%2020260316190631.png)
![Pasted image 20260316190623.png](Pasted%20image%2020260316190623.png)
![Pasted image 20260316190614.png](Pasted%20image%2020260316190614.png)
![Pasted image 20260316190541.png](Pasted%20image%2020260316190541.png)
![Pasted image 20260316190604.png](Pasted%20image%2020260316190604.png)
```
````tabs
tab: Recursive
```java
public class Tree {
	public static void preOrder(Node node, ArrayList<Integer> res) {
        if (node == null)
            return;

        // Visit the current node first
        res.add(node.data);

        // Traverse the left subtree
        preOrder(node.left, res);

        // Traverse the right subtree
        preOrder(node.right, res);
    }
}
```
tab: Iterative
```java
public class Tree {
	public static List<Integer> preOrder(Node root) {
        List<Integer> res = new ArrayList<>();
        if (root == null)
            return res;

        Stack<Node> s = new Stack<>();
        s.push(root);

        while (!s.isEmpty()) {
            Node curr = s.pop();
            res.add(curr.data);

            if (curr.right != null)
                s.push(curr.right);
            if (curr.left != null)
                s.push(curr.left);
        }

        return res;
    }
}
```
````
## PostOrder Traversal
>[!tldr] Left -> Right -> Root

```media-slider
![Pasted image 20260316190835.png](Pasted%20image%2020260316190835.png)
![Pasted image 20260316190841.png](Pasted%20image%2020260316190841.png)
![Pasted image 20260316190849.png](Pasted%20image%2020260316190849.png)
![Pasted image 20260316190855.png](Pasted%20image%2020260316190855.png)
![Pasted image 20260316190902.png](Pasted%20image%2020260316190902.png)
![Pasted image 20260316190909.png](Pasted%20image%2020260316190909.png)
```
````tabs 
tab: Recursive```
```java
public class Tree {
	static void postOrder(Node node, ArrayList<Integer> res) {
        if (node == null)
            return;

        // First we traverse left subtree
        postOrder(node.left, res);

        // After visiting left, traverse right subtree
        postOrder(node.right, res);

        // now we visit node
        res.add(node.data);
    }
}
```
tab: Iterative
The traversal order in postorder is left child, right child, and then the node itself. Instead of recursion, the algorithm simulates the recursive calls using a stack. 
	1. It first pushes nodes onto the stack as it moves down the left side of the tree. 
	   2. Once it reaches a leaf, it starts processing the nodes by checking if the right child has been visited. 
	      3. If not, it traverses the right subtree; otherwise, it processes the node and adds it to the result.
```java
public class Tree {
	static List<Integer> postOrder(Node root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;

        Stack<Node> s = new Stack<>();
        Node lastVisited = null;

        while (!s.isEmpty() || root != null) {
            if (root != null) {
                s.push(root);
                root = root.left;
            } else {
                Node peekNode = s.peek();
                if (peekNode.right != null && lastVisited != peekNode.right) {
                    root = peekNode.right;
                } else {
                    res.add(peekNode.data);
                    lastVisited = s.pop();
                }
            }
        }
        return res;
    }
}
```
````

## Boundary Traversal
>[!info]
>The boundary of a binary tree consists of the following parts:
>1. ***Left Boundary:*** The nodes on the left edge of the tree, excluding the leaf nodes.
>2. ***Leaf Nodes:*** All the leaf nodes from left to right.
>3. ***Right Boundary:*** The nodes on the right edge of the tree, excluding the leaf nodes, traversed in bottom-up order.
>![Pasted image 20260317161701](Assets/Pasted%20image%2020260317161701.png)

```media-slider
![Pasted image 20260317161818.png](Pasted%20image%2020260317161818.png)
![Pasted image 20260317161824.png](Pasted%20image%2020260317161824.png)
![Pasted image 20260317161833.png](Pasted%20image%2020260317161833.png)
```

```java
public class Tree {
	boolean isLeaf(Node node) {
        return node.left == null && node.right == null;
    }
    
    // left boundary of the tree 
    void addLeftBoundary(Node root, List<Integer> res) { Node curr = root.left; while (curr != null) { 
    // If the current node is not a leaf, 
    // add its value to the result 
	    if (!isLeaf(curr)) { res.add(curr.data); } 
    
    // Move to the left child if it exists, 
    // otherwise move to the right child 
	    if (curr.left != null) { 
		    curr = curr.left; 
		} else { 
			curr = curr.right; 
		} 
	} 
	
	// right boundary of the tree
	void addRightBoundary(Node root, List<Integer> res) {
        Node curr = root.right;
        List<Integer> temp = new ArrayList<>();
        while (curr != null) {
            // If the current node is not a leaf,
            // add its value to a temporary list
            if (!isLeaf(curr)) {
                temp.add(curr.data);
            }
            // Move to the right child if it exists,
            // otherwise move to the left child
            if (curr.right != null) {
                curr = curr.right;
            } else {
                curr = curr.left;
            }
        }
        // Reverse and add the values from
        // the temporary list to the result
        for (int i = temp.size() - 1; i >= 0; --i) {
            res.add(temp.get(i));
        }
    }
    
    // leaves of the tree
    void addLeaves(Node root, List<Integer> res) {
        // If the current node is a
        // leaf, add its value to the result
        if (isLeaf(root)) {
            res.add(root.data);
            return;
        }
        // Recursively add leaves of
        // the left and right subtrees
        if (root.left != null) {
            addLeaves(root.left, res);
        }
        if (root.right != null) {
            addLeaves(root.right, res);
        }
    }
    
    // boundary traversal of the binary tree
    List<Integer> printBoundary(Node root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        // If the root is not a leaf,
        // add its value to the result
        if (!isLeaf(root)) {
            res.add(root.data);
        }

        // Add the left boundary, leaves,
        // and right boundary in order
        addLeftBoundary(root, res);
        addLeaves(root, res);
        addRightBoundary(root, res);

        return res;
    }
}
```
# References
1. https://www.geeksforgeeks.org/dsa/tree-traversals-inorder-preorder-and-postorder/
2. https://www.geeksforgeeks.org/dsa/boundary-traversal-of-binary-tree/