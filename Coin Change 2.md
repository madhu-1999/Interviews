#dsa #dp 
# Problem
Given coins of different denominations and a total amount of money. Find the number of combinations that make up given amount. Assume unlimited instances of each coin.
>[!example]+
>amount = 5
>coins = [1, 2, 5]
>O/P: 4
>Soln: 5=5
>5 = 2+2+1
>5 = 2+1+1+1
>5 = 1+1+1+1+1
# Solution
==[[Knapsack#Unbounded Knapsack|Unbounded Knapsack]]== problem.
## Recurrence Relation
$$dp(c,\text{amt})= \begin{cases} 0 & c=0,\text{amt} > 0\\1 & \text{amt} = 0\\dp(c-1, \text{amt}) & c > \text{amt}\\ dp(c-1,\text{amt} + dp(c, amt-c)) & c\leq amt\end{cases}$$
where $c$ = coin denomination