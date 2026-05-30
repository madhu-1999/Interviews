#dp #dsa 
# Problem
[Leetcode](https://leetcode.com/problems/interleaving-string/description/)
Given strings _s1, s2_ and _s3_, find whether _s3_ is formed by an interleaving of _s1_ and _s2_.
An interleaving of _s_ and _t_  is a configuration where  _s_ and _t_ are divided into substrings  such that:
$$
\begin{matrix}
s = s_1 + s_2+s_3 +...+s_n\\
t =t_1 + t_2 + t_3 + ... + t_n\\
|n-m| <= 1
\end{matrix}
$$
The interleaving is $s_1 + t_1 + s_2 + t_2 + ...$ or $t_1 + s_1 + t_2 + s_2 + ...$ 
>[!example]
>s1 = "aabcc"
>s2 = "dbbca"
>s3 = "aadbbcbcac"
>O/P: true
>
![](https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg)

# [Solution](https://leetcode.com/problems/interleaving-string/solutions/7129553/find-if-its-interleaving-string-dynamic-programming/)
1. Length(s) + Length(t) = Length(s3)
2. Ordering of substrings $s_1, s_2, ...s_n$ and $t_1, t_2, ...,t_n$ must be preserved. i.e. $s_1$ must appear before $s_2$ and so on.
## Why DP?
$s_3[0...i+j]$ (inclusive) is an interleaving of _s_ and _t_ iff:
1. $s_3[i+j] = s[i]\space \text{or}\space t[j]$ 
2. If $s[0...i-1]$ and $t[0...j-1]$  form the interleaving string $s_3[0...(i+j-1)]$ .
We can see that subproblems are repeating and the value of subproblems determines value of the problem. Thus conditions for DP are satisfied.
## Recurrence Relation
$$
dp(i, j) = \begin{cases}\text{true}& i=0,j=0\\
dp(i-1,0) \land s[i] = s_3[i] & i\neq 0,j=0\\
dp(0,j-1)\land t[j] = s_3[j] & i=0,j\neq0\\
(dp(i-1, j)\land s[i] = s_3[i+j])\lor (dp[i, j-1]\land t[j]=s_3[i+j])& i\neq0,j\neq0
\end{cases}
$$