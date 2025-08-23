#dsa #dp 
# Problem 
 given a list of non-negative integers $nums$ and a target $S$, we have two symbols $+$ and $-$ For each integer in $nums$, choose to assign $+$ or $-$ to it. Then find the no of ways to assign symbols such that sum of all integers is $sum$.
 >[!example]+
 >nums=[1, 1, 1, 1, 1]
 >sum=3
 >O/P: 5
 >Soln:
 >-1+1+1+1+1=3
 >1-1+1+1+1=3
 >1+1-1+1+1=3
 >1+1+1-1+1=3
 >1+1+1+1-1=3
# Solution
Assume all negative integers in one set and all positive in second set. 
Then problem becomes, find all subsets such that $s_1 - s_2 = sum$ i.e [[No of subsets with given difference]].