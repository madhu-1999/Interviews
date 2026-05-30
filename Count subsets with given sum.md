#dsa #dp 
# Problem
Given a **non-empty** array $nums$ containing only positive integers and a $sum$, find the no of subsets of $nums$ with given $sum$.
>[!example]+
>nums = [3, 1, 2, 3]
>sum = 6
>O/p: 3
>{3, 1, 2} , {3, 3}, {1, 2, 3}
# Solution
==Variation of [[Subset Sum]]==. Instead of finding T/F value we add to count
## Recurrence Relation
$$dp(i, sum) =\begin{cases} 1 & sum=0\\0  & i=0,sum\neq 0\\dp(i-1, sum) & i > sum\\dp(i-1,sum)+ dp(i-1,sum-i) & i\leq sum\end{cases}$$
where $i$ = i-th element in set.
# Time Complexity
$O(n\times sum)$ where $n$ = no of elements