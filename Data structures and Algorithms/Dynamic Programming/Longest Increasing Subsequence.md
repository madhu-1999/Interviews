#dsa #dp 
# Problem
[Leetcode](https://leetcode.com/problems/longest-increasing-subsequence/description/)
Given an integer array `nums`, return _the length of the longest **strictly increasing**_ _**subsequence**_.
>[!example]
>**Input:** nums = [10,9,2,5,3,7,101,18]
**Output:** 4
**Explanation:** The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
# Solution
Variation of [[LCS]]
Note: We loop over $i=0...n-1$ and $j=i+1...n-1$.
$$dp(j) = \begin{cases}
1 & i = 0 \space\text{(Base case)}\\
max (dp(i) + 1, dp(j)) & nums[i] < nums[j]\\
max(1, dp(j)) & otherwise \space\text{(1 because nums[j] can be start of a new subsequence if no nums[i], i< j is smaller than nums[j])}
\end{cases}$$