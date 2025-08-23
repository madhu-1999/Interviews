#dsa #dp 
# Problem
Given a **non-empty** array $nums$ containing only positive integers, find if the array can be partitioned into two subsets such that sum of elements in both subsets is equal.
>[!example]+
>nums=[1, 5, 11, 5]
>O/P: True
>Array can be partitioned into [1, 5, 5] and [11].

# Solution
1. Find sum of all elements in $nums$ => $totalSum$. 
	1. If $totalSum$ is odd, no partition is possible.
	2. If $totalSum$ is even, partition is possible.
2. If partition is possible this problem can be reduced to finding if a subset exists with sum of $totalSum/2$ which is the [[Subset Sum]] problem.