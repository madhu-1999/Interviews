#dsa #dp 
# General Problem
finding the maximum value $Z$  by selecting items $i$ with weights $w_i$ and profits $p_i$ such that their total weight is less than or equal to the knapsack's capacity $W$ , and the total profit is maximized, expressed as:
Maximize $\sum_{i=1}^n p_ix_i$ subject to  $\sum_{i=1}^n w_ix_i \leq W$ where $x_i \in \{0,1\}$ for each item $i$  
==How the bag is filled depends on knapsack type==
## Types
[[#0-1 Knapsack]]
[[#Bounded Knapsack]]
[[#Unbounded Knapsack]]
[[#Fractional Knapsack]]
[#Integer Knapsack]]
# 0-1 Knapsack
Refer [[#General Problem]]
Constraints:
	Each item can be picked only once if selected. Can reject element also
	 We can't pick more than size of knapsack
Brute Force Time Complexity = O(2<sup>n</sup>) for n items.
>[!example]+
> wt = [3, 4, 1]
> profit = [5, 6, 2]
> bag = 6
> Ans: Pick elements with wt= {4, 1} to get max profit 8. Bag unused = 6 - (4 + 1) = 1.

## Recurrence Relation
$$ Z(i,W) = \begin{cases} 0 & \text{i=0, W=0}\\ Z(i-1,W) & w_i > W\\ max(p_i + Z(i-1,W-w_i), Z(i-1,W)) & w_i \leq W \end{cases}$$

# Fractional Knapsack
Refer [[#General Problem]]
Constraints:
	Each item can be picked once but we can put some fraction of item in bag
	Can't pick more than size of bag.
>[!example]+
>wt=[3, 4, 2]
>profit=[5, 6, 4]
>bag = 6
>Ans: rate = [3/5, 4/6, 2/4] = [1.67, 1.5, 2]
>Greedy approach so pick from highest rate:
>Pick item with wt 2 max_profit = 4 bag = 6-2 = 4
>Pick item with wt 3 max_profit = 4 + 5 = 9 bag = 4-3 = 1
>Pick item with wt 4 max_profit = 9 + 1x1. 5 = 10.5 bag = 1-1 = 0

# Bounded Knapsack
Refer [[#General Problem]]
Constraints:
	Each item can be included 0 or more times upto a limit.
	Can't pick more than size of bag.
# Unbounded Knapsack
Refer [[#General Problem]]
Constraints:
	Each item can be included 0 or more times (no limit)
	Can't pick more than size of bag.
# Variations of Knapsack
+ Mention some sort of *capacity* and array of items where we can choose to ***INCLUDE*** or ***EXCLUDE*** each item. No of times to choose depends on problem.
[[Subset Sum]]
[[Partition Equal Subset Sum]]
[[Count subsets with given sum]]
[[Minimum subset sum difference]]
[[No of subsets with given difference]]
[[Target Sum]]
[[Coin Change]]
[[Coin Change 2]]
[[Rod Cutting Problem]]