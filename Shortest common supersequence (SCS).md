#dsa #dp 
# Problem
[Leetcode](https://leetcode.com/problems/shortest-common-supersequence/description/)
Given two strings `str1` and `str2`, return _the shortest string that has both_ `str1` _and_ `str2` _as **subsequences**_. If there are multiple valid strings, return **any** of them.
>[!example]
>str1 = "abac", str2 = "cab"
>O/P: "cabac"
>str1 = "abac" is a subsequence of "cabac" because we can delete the first "c".
str2 = "cab" is a subsequence of "cabac" because we can delete the last "ac".
The answer provided is the shortest such string that satisfies these properties.

# [Solution](https://www.youtube.com/watch?v=pHXntFeu6f8&list=PLEJXowNB4kPxBwaXtRO1qFLpCzF75DYrS&index=20)
This is a variation of [[LCS]].
To get SCS, we need to find LCS(str1, str2) and then backtrack on the dp array to get the SCS.
	If $str1[i] = str2[j]$ => include the character only once in the SCS.
	If $str1[i] \neq str2[j]$  => include $str1[i]$ iff $dp[i-1][j] > dp[i][j-1]$ else $str2[j]$ (Move in dir of larger dp value)
Repeat until either string is exhausted, then add remaining characters in other string.
Reverse the result (since we built it bottom up) and return.