#dp #dsa 
# Problem
Given a set of non-negative integers & *sum*, find if a subset of the set can have addition value *sum*.
Each number in the set can be chosen only once.
>[!example]+
>nums = [1, 5, 6, 3]  sum = 8
> subset = {5, 3} adds up to 8 so o/p: True

# Recurrence Relation 
$$dp(i, sum) =\begin{cases}\text{true} & sum=0\\\text{false}  & i=0,sum\neq 0\\dp(i-1, sum) & i > sum\\dp(i-1,sum) || dp(i-1,sum-i) & i\leq sum\end{cases}$$ where $i$ = i-th element in set.
# Time Complexity
$O(n\times sum)$ where $n$ = no of elements
# 
==Note: This is straightforwardly a [[Knapsack#0-1 Knapsack|0/1 knapsack]] problem.==