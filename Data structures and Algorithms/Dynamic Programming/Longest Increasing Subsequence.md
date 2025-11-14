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
Time complexity: O(n<sup>2</sup>) 

## Using Binary Search (O(nlogn))
We maintain an array `tails[]` where:
- `tails[i]` = the smallest possible tail (ending value) of an increasing subsequence of length `i+1`.    

We iterate through each `num` in `nums`:
1. Use **binary search** to find where `num` should go in `tails`.  
2. If `num` is larger than all tails, append it (increase length).
3. Otherwise, replace the first element in `tails` that is **≥ num** with `num`.

>[!example]
>|num|tails (after step)|explanation|
|---|---|---|
|10|[10]|start with 10|
|9|[9]|replace 10 → smaller tail|
|2|[2]|replace 9|
|5|[2, 5]|append (increasing)|
|3|[2, 3]|replace 5|
|7|[2, 3, 7]|append|
|101|[2, 3, 7, 101]|append|
|18|[2, 3, 7, 18]|replace 101|

```java
private int lengthOfLIS(int[] nums) {
    ArrayList<Integer> tails = new ArrayList<>();
	for (int num : nums) {
		// Find the index where 'num' can be inserted to maintain sorted order
		// or if 'num' is already present.
		// If num is greater than all elements, 'idx' will be negative,
		// and the insertion point will be '-(idx + 1)', which is tails.size().
		int idx = Collections.binarySearch(tails, num);

		if (idx < 0) {
			// If num is not found, get the insertion point
			idx = -(idx + 1);
		}

		if (idx == tails.size()) {
			// If num is greater than all elements in 'tails', extend the LIS
			tails.add(num);
		} else {
			// Otherwise, replace the element at 'idx' with 'num'.
			// This means we found a smaller tail for an increasing subsequence
			// of the same length, which is beneficial.
			tails.set(idx, num);
		}
	}

    return tails.size();
}
```
