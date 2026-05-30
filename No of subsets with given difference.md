#dsa #dp 
# Problem
Given a **non-empty** array $nums$ containing only positive integers, find no of ways the array can be partitioned into two subsets such that difference between subset sums is $\text{diff}$
>[!example]+
>nums = [3, 2, 1, 3]
>diff = 3
>O/p=3
>Soln: {3,1, 2} & {3} , {3, 3} & {1, 2}, {1, 2, 3} & {3}
# Solution
If sum of first subset is given by $s_1$ and second by $s_2$ , diff is denoted by $d$ and total sum by $s$ then: $$s_1 - s_2 = d$$ $$s_1 + s_2 = s$$ 
On adding both equations we get: $s_1 = \frac{d+s}{2}$ .
Then this problem, reduces to [[Count subsets with given sum]].