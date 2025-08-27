#data-science #statistics 
```table-of-contents
title: index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 3 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Prerequisite
[[Probability#Random Variable|Random Variable]]
[[Probability#Joint Probability|Joint Distribution]]
[[Population vs Sample]] 
# Covariance
+ Measure which indicates how two random variables change together.
	+ +ve => Both inc/dec simultaneously
	+ -ve => One inc, other dec simultaneously.
	+ 0 => no ***linear*** relationship i.e non-linear relationship possible.
## Why it matters
1. Direction of ***linear*** relationship between variables.
2. Strength of the relationship, in ***unstandardized*** form.
3. Foundation for calculating [[#Correlation]]

## Discrete Random Variables
$$Cov(X,Y) = \sum_x\sum_y(x-\mu_x)(y-\mu_y)f(x,y)$$
## Continuous Random Variables
$$Cov(X,Y) = \int_{-\infty}^{\infty}\int_{-\infty}^{\infty}(x-\mu_x)(y-\mu_y)f(x,y)dxdy$$
## 
$$Cov(X,Y) = E[(X-E(X))(Y-E(Y))]$$
$$= E(XY) - E(X)E(Y)$$

## Formula for population and sample
$$\text{Cov}(X,Y) =\frac{1}{N}\sum_{i=1}^N(X_i-\mu_x)(Y_i-\mu_y) $$
where $N$ = population size, $\mu_x,\mu_y$ = means (or [[Probability#Expectation E(X)|expectation]]) of X, Y.
$$\text{Cov}(X,Y) =\frac{1}{n-1}\sum_{i=1}^n(X_i-\bar{x})(Y_i-\bar{y}) $$
where $n$ = sample size, $\bar{x},\bar{y}$ = mean of X and Y.
***NOTE***: Joint probability is assumed to be $\frac{1}{N}$ or $\frac{1}{n-1}$ .
### **(Optional) Why $n-1$ instead of $n$ ?**
+ When we calculate sample means $\bar{x},\bar{y}$ , they are always closer to the sample's data points than the true population means $\mu_x,\mu_y$ .
+ Thus, the deviation of each data point from the sample mean $X_i-\bar{x}$ and $Y_i-\bar{y}$ tends to be smaller than deviation from population means  $X_i-\mu_x$ and $Y_i-\mu_y$ .
+ If we divide it by $n$, the result would, on average be an underestimation of the population covariance.
+ Dividing by $n-1$ instead inflates the result just enough to compensate for the underestimation.
#
![Covariance Meaning](http://t1.gstatic.com/images?q=tbn:ANd9GcSwTrCFPSPc4cRR8lRyansMIrO7IdcDytzztd7KwY1d10WhQiRm1HcMcIR92XrLM0ie0ihgxzCW)
## Disadvantages
1. Sensitive to scale of variables. Large values can inflate result.
2. Does not indicate strength of relationship in a ***standardized*** way.
3. Outliers can significantly affect covariance value.
# Correlation
+ Measure which indicates how two random variables change together.
+ Strength is measured by **correlation coefficient** which ranges from -1 to +1.
	+ Strong +ve correlation => both inc/dec simultaneously => close to +1
	+ Strong -ve correlation => one inc, other dec simultaneously => close to -1
	+ little to no relationship => close to 0
+ Measures strength of relationship on a ***normalized*** scale.
+ Type of relationship measured (linear, monotonic etc..) depends on correlation coefficient.
>[!warning]+
>+ Overestimates relationship if sample is small. Coefficient of determination (R<sup>2</sup>) is a better indicator.
>+ Correlation ***DOES NOT*** imply causation
>	+ change in one variable does not necessarily cause change in the other. 
>	+ An unobserved third variable might be the cause of the relationship.
>	>[!example]+
>	 > Ice cream sales and swimming both increase in summer, but neither causes the other; the true reason is hot weather


| \|r\|     | Strength of correlation |
| --------- | ----------------------- |
| 0.0 < 0.1 | no correlation          |
| 0.1 < 0,3 | little                  |
| 0.3 < 0.5 | medium                  |
| 0.5 < 0.7 | high                    |
| 0.7 < 1   | very high               |
## Pearson Correlation Coefficient
$$\rho_{XY} = \frac{Cov(X,Y)}{\sigma_x.\sigma_y}$$
where $\sigma_x,\sigma_y$ = population standard deviation of X and Y.
$$r_{XY} = \frac{Cov(X,Y)}{s_X.s_y}$$
where $s_x,s_y$ = sample standard deviation of X and Y
### **Assumptions** 
1. The two variables need to be [[Data & AI/Data types#Continuous|continuous]] (interval or ratio).
2. The two variables should have a linear relationship. (check scatterplot)
3. No spurious outliers (check [[#Covariance]] for reason).
4. The variables should be [[Probability Distributions#Normal / Gaussian Distribution|normally]] or near-to normally distributed.
## Spearman rank correlation coefficient
+ Use if :
	+ atleast one variable is [[Data & AI/Data types#Ordinal|ordinal]]
	+ one or more variables do not follow [[Probability Distributions#Normal / Gaussian Distribution|normal distribution]].
+ Measures **monotonicity** of relationship between two random variables.
>[!info]+
>In a monotonic relationship, rate of change is different for both variables, unlike linear relationship where rate of change a.k.a slope is constant.
>+ +ve monotonic: both increase => close to +1
>+ -ve monotonic: one inc, other dec => close to -1
>+ 0: no monotonic relationship
>
>![Graphs showing a positive, negative, and zero monotonic relationship](https://www.scribbr.de/wp-content/uploads/2021/08/monotonic-relationships.png)

## Formula for population and sample
+ First rank data from each variable separately from low to high. Every datapoint gets a rank from first, second, third etc.
+ The find difference $d_i$ between ranks of X and Y pairs.
$$\rho = \frac{6\sum d_i^2}{N^3 - N}$$
where $d_i=\text{rank}(X_i) - \text{rank}(Y_i)$ , $N$ = population size.
+ Same formula for sample.
### ***Alternative formula***
[[#Pearson Correlation Coefficient]] transformed:
$$\rho = \frac{Cov(R_X,R_Y)}{\sigma_{R_X}\sigma_{R_Y}}$$
where $R_X,R_Y$ = rank variables of X and Y 
