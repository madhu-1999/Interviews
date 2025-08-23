#dsa #dp 
# Problem
Given a rod of length ***n inches*** and an array ***price[]***.  ***price[i]*** denotes the value of a ***piece*** of length i. The task is to determine the ***maximum*** value obtainable by ***cutting up the rod*** and selling the pieces. price[] is ***1-indexed*** array.
>[!example]+
>price = [1, 5, 8, 9]
>L = 4
>O/P:10
>Cut into two pieces of size 2. max-profit = 5 + 5 = 10

# Solution
==[[Knapsack#Unbounded Knapsack|Unbounded Knapsack]]== problem. 
## Recurrence relation
$$dp(i, L)= \begin{cases} 0 & i=0\space\text{or}\space L = 0\\ dp(i-1, L) & i > L\\ max(dp(i-1,L), profit[i] + dp(i, L-i)) & i\leq L\end{cases}$$
where $i$ is length of piece of rod, $L$ = total length of rod, $profit[i]$ = profit from selling piece of rod of length $i$.
