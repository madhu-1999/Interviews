#dsa #dp 
# Problem
Given a **non-empty** array $nums$ containing only positive integers, find if the array can be partitioned into two subsets such that the difference between subset sums is minimum.
>[!example]+
>nums = [1, 6, 11, 5]
>Ans: 1
>Soln: {1, 6, 5} & {11} => 12 - 11 = 1
# Solution
==Variation of [[Partition Equal Subset Sum]].==  Find subset with largest $sum \leq totalSum/2$ .
then $\text{diff} =  abs(sum - (totalSum - sum))$ .
If $sum = totalSum/2$ then diff = 0 , thus we have to find largest $sum \leq totalSum/2$ .