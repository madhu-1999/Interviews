#dsa 
+ A [[Binary Tree]] in which a node is greater than all nodes in its left subtree but smaller then all nodes in right subtree
# Representation
```java
public class TreeNode{
	int data;
	TreeNode left;
	TreeNode right;
}
```
# Find min
```java
public int findMin(TreeNode root) {
	if (root.left != null)
		return findMin(root.left);
	return root.data;
}
```
# Find max
```java
public int findMax(TreeNode root) {
	if(root.right != null)
		return findMax(root.right);
	return root.data;
}
```
# Search
```java
public boolean search(TreeNode root, int target) {
	if(root.data == target)
		return true;
	if(root.data > target && root.left != null){
		return search(root.left, target);
	} else {
		return search(root.right, target);
	}
	return false;
}
```
# Insert
```java
public void insert(TreeNode root, TreeNode node) {
	if(node.data > root.data) {
		if(root.left != null){
			insert(root.left, node);
		} else {
			root.left = node;
		}
	} else {
		if(root.right != null){
			insert(root.right, node);
		} else {
			root.right = node;
		}
	}
}
```
# Time complexity
h = height of tree

| ***Operations*** | ***Time Complexity*** | ***Space Complexity*** |
| ---------------- | --------------------- | ---------------------- |
| findMin          | O(h)                  | O(h)                   |
| findMax          | O(h)                  | O(h)                   |
| Search           | O(h)                  | O(h)                   |
| Insert           | O(h)                  | O(h)                   |
