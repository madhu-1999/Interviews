#data-science #statistics
# Prerequisite
+ [[Data & AI/Data types|Data types]]
# Definition
+ Used to summarize and describe variable(s) for a sample of data.
+ Cannot be used to make inferences or predictions, unlike [[Inferential Statistics]]
+ Summarize one variable -> **univariate analysis**
+ Analyze relationship b/w 2 variables -> **bivariate analysis**
+ Analyze relationship b/w 3+ variables -> **multivariate analysis**
# Types of Descriptive Statistics
## Measures of Central Tendency
+ Describe **where the data centers** in the dataset.
+ Help understand data distribution
	+ Ex: mean can reveal trend, median can highlight skewed distribution.
### **Mean**
$$
\bar{x} = \frac {\sum x} {n}
$$
$x \rightarrow observation$
$n \rightarrow \text{total number of observations}$
+ Average value of sample
### **Mode**
+ Most frequently occurring value in sample.
### **Median**
+ Middle value of sorted sample
## Measure of Variability
+ Describes **how the data spreads out**.
+ Identify outliers, understand data variability w.r.t mean
+ Variability measures are important in residual analysis to check how well a model fits the data.
### **Range**
$$ Range(x) = max(x) - min(x) $$
+ Bigger the range, more spread out the data is.
+ **Sensitive to outliers**
+ Needs to be used with other statistical measures to see the full picture.
### **Variance**
+ Average squared deviation from the mean.
$$ \sigma^2 = \frac{\sum{(x - \bar{x})^2}}{N}$$
$\bar{x} \rightarrow mean$
$x \rightarrow observation$
$N \rightarrow \text{total number of observations}$
### **Standard deviation
+ Square root of variance.
$$\sigma = \sqrt{\frac{\sum{(x - \bar{x})^2}}{N}}$$
$\bar{x} \rightarrow mean$
$x \rightarrow observation$
$N \rightarrow \text{total number of observations}$
# Measures of frequency distribution
+ Describes **how data points are distributed across categories/intervals.
+ Helps identify patterns, outliers and overall structure of dataset.
+ Includes measure like:
	- Data intervals or categories
	- Frequency counts
	- Relative frequencies (percentages)
	- Cumulative frequencies when needed

+ Create viz like histogram, pie charts.