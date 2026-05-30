#math #data-science 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Multiplication Rule
If we make $n$ choices with,
	$k_1$ options for first choice,
	$k_2$ options for second choice etc..
then, there are $k_1,\times k_2 \times ...\times k_n$ possible ways to make these choices.

Ex: How many possible license plates could be stamped if each license plate were required to have exactly 3 letters and 4 numbers? (With replacement)
Ans:  Selecting letters x Selecting numbers
$$(26\times 26\times 26)\times (10\times10\times 10\times 10) = 175,650,000$$
Ex: Same question, but choose 3 unique letters and 4 unique numbers. (Without replacement)
Ans: $$(26\times 25\times 24)\times (10\times9\times 8\times 7) = 78,624,000$$
***Note*** Remember **AND** => x and **OR** => + 
# Permutations
+ ***Arrangement*** of objects.
+ Order matters
$$^nP_r = \frac{n!}{(n-r)!}$$
## Properties
+ $^nP_0 = 1$
+ $^nP_1 = n$
+ $\frac{^nP_r}{^nP_{r-1}} = n - r+1$
## Important formulae
+ Arrange $n$ objects of which $r$ are the same and $q$ are the same: 
$$\frac{n!}{r!\times q!}$$
+ Arrange $n$ objects in a circle: $(n-1)!$
+ Arrange $r$ things out of $n$ things in a circle: $^nC_r \times (r-1)!$
+ If anti-clockwise and clockwise permutations are considered to be the same: $$\frac{1}{2}\times(n-1)!$$
## Examples
1.  How many 3-digit numbers greater than 500 can be formed using 3, 4, 5, and 7? (With repetition)
Ans: 2 choices for hundreds digit (5,7). 4 choices for tens and ones digits.
$$2\times 4\times 4 = 32$$
2. How many 3-digit numbers divisible by 3 can be formed using the  digits 2, 4, 6, and 8 without repetition?
Ans: For a number to be divisible by 3, the sum of its digits must be divisible by 3.
Thus, we have 2 combinations 2, 4, 6 **or** 4, 6, 8.
Each group can be arranged in 3! ways
$$3! + 3! = 12$$
# Combination
+ Choose/select objects.
+ Order doesn't matter
$$^nC_r = \frac{n!}{(n-r)!\times r!}$$
## Properties
+ $^nC_0 = ^nC_n = 1$
+ $^nC_1 = n$
+ $^nC_r + ^nC_{r-1} =  ^{n+1}C_r$
+ $^nC_0 + ^nC_1 + ...+^nC_n = 2^n$
+ $^nC_r = ^nC_{n-r}$
## Important Formulae
+ Total no of ways to distribute $n$ identical items among $p$ people so that each person gets any number of items is : $$^{n+p-1}C_{p-1}$$
+ Total no of ways to distribute $n$ identical items among $p$ people so that each person gets atleast one item is : $$^{n-1}C_{p-1}$$
+ Total number of ways of dividing $(m+n+p)$ distinct items into three unequal groups is: $$\frac{(m+n+p)!}{m!\times n!\times p!}$$
