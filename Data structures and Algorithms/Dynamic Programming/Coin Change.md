#dsa #dp 
# Problem
Given an array of denominations $\text{coins}$. Find the minimum no of coins needed to get $\text{amount}$  , such that each coin in $\text{coins}$ can be selected unlimited times. Return -1 if not possible.
>[!example]+
>coins=[1, 2, 5]
>amount = 11
>O/P:3
>Ans: [5, 5, 1]

# Solution
==[[Knapsack#Unbounded Knapsack|Unbounded Knapsack]]== problem. 
## Recurrence relation
$$dp(c,\text{amt})= \begin{cases} \infty & c=0,\text{amt} > 0\\0 & \text{amt} = 0\\dp(c-1, \text{amt}) & c > \text{amt}\\ min(dp(c-1,\text{amt}), 1 + dp(c, amt-c)) & c\leq amt\end{cases}$$
where $c$ = coin denomination
