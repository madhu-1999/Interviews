#dsa #dp 
# Problem
[Leetcode](https://leetcode.com/problems/longest-common-subsequence/description/)
Given two strings _text1_ and _text2_, return the length of their longest **common subsequence**. If there is no **common subsequence**, return 0.
>[!example]
>text1 = "abcde"
>text2 = "ace"
>O/P:3
>The longest common subsequence is "ace" and its length is 3.

# Solution
$$dp(i,j) = \begin{cases}
0 & i=0\space\text{or}\space j=0\\
dp(i-1, j-1) + 1 & \text{text1[i]} = \text{text2[j]}\\
max(dp(i,j-1), dp(i-1,j)) & \text{text1[i]}\neq\text{text2[j]}
\end{cases}$$