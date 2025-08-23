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
# Prerequisite
+ [[Probability#Random Variable|Random Variable]]
# Hypergeometric Distribution
+ Discrete distribution
If we randomly select $n$ items **without replacement** from a set of $N$ items such that $m$ items are of one type and $N-m$ of a second type,
then the **PMF** of the discrete random variable X is called the hypergeometric distribution.
$$P(X=x) = \frac{^mC_x \times ^{N-m}C_{n-x}}{^NC_n}$$
Ex: What is the probability of drawing 8 women for a jury of 13 when there are 25 female and 25 male candidates? Assume jurors are randomly selected.
Ans: $N$ = 50, $m$ = 25 (female), $n$ = 13 (jury size), $X$ = 8
$$P(X=8) = \frac{^{25}C_8\times ^{25}C_5}{^{50}C_8}$$
![Hypergeometric distribution graph.](https://i0.wp.com/statisticsbyjim.com/wp-content/uploads/2022/10/hypergeometric_distribution_graph.png?resize=576%2C384&ssl=1)


# Binomial Distribution
+ Discrete distribution
+ Conditions 
	1. Fixed Number of trials
	2. Two possible outcomes, often labelled success and failure
	3. Independent trials i.e. outcome of each trial is independent of the others (event is independent or sampling with replacement).
	4. Constant probability i.e. the probability of success remains the same for each trial.
		Ex independent event but changing probability: In a series of online quizzes, the questions in one quiz might be designed to be completely independent of the questions in the next quiz. However, the difficulty level could increase with each quiz, changing the probability of a student getting a perfect score
$$P(X = r) = ^nC_r\times p^r \times (1-p)^{n-r}$$
where $n$ = Total number of trials
$r$ = number of successes
$p$ = Probability of success

Ex: A fair coin is flipped 25 times. What is the probability of getting exactly 10 heads?
Ans: $$P(X=10) = ^{20}C_{10} \times (0.5)^{10} \times (1 - 0.5)^{15}$$
Mean/E(X): $\mu = n\times p$
Variance: $np(1-p)$
# Negative Binomial Distribution
See [[Probability Distributions#Binomial Distribution| Binomial Distribution]] first
Used to model the **number of failures** needed before achieving a **certain number of successes** in a sequence of independent trials, where the probability of success in each trial is constant.
$$P(X = k) = ^{k + r - 1}C_{k}\times p^r\times (1-p)^k$$
where $k$ = Number of failures
$r$ = Desired number of successes
$p$ = Probability of success in a trial

Ex: If we need 3 defective units and each unit has a 10% chance of being defective. What is the probability of getting 5 non defective units before getting the third defective one?
$p$ = 0.1, $r$ = 3, $k$ = 5
$$P(X=5) =^{5+3-1}C_5\times (0.1)^3 \times (0.9)^5=0.0124$$
Mean/E(X): $$\mu = \frac{r(1-p)}{p}$$
Variance: $$\sigma^2 = \frac{r(1-p)}{p^2}$$
Use for **over dispersed** data i.e $\sigma^2 > \mu$ 
# Geometric Distribution
Used to model the **number of failures** needed before achieving **first success** in a sequence of independent trials, where the probability of success in each trial is constant.
+ Special case of [[Probability Distributions#Negative Binomial Distribution| Negative Binomial Distribution]] where $r$ = 1
$$P(X = x) = (1-p)^{x-1}p$$
where $x$ = no of failures
$E(X) = \frac{1}{p}$
$$\mu = \frac{1-p}{p}$$
$$\sigma^2 = \frac{1-p}{p^2}$$
Ex:A light bulb manufacturing factory finds 3 in every 60 light bulbs defective. What is the probability that the first defective light bulb with be found when the 6th one is tested?
Ans: $p = \frac{3}{60} = 0.05$
$$P(X=6) = (1-0.5)^5(0.05) = 0.0386$$
# Uniform Distribution
+ Can be discrete or continuous
A probability distribution where all outcomes are equally likely to occur.
Ex: Rolling a fair six sided die![Uniform-Distribution-1](https://media.geeksforgeeks.org/wp-content/uploads/20250412145009731320/Uniform-Distribution-1.webp)
$$P(X = x_i) = \frac{1}{n} \space\space\forall\space i = 1,2,3...,n$$
Mean/E(X): $\mu = \frac{min(i) +max(i)}{2}$
Variance: $\sigma^2 = \frac{(n^2-1)}{12}$

Random number generation in range [0,1]
![Uniform-Distribution-2](https://media.geeksforgeeks.org/wp-content/uploads/20250412145009880417/Uniform-Distribution-2.webp)
$$P(X) = \frac{1}{b-a} \space \text{for}\space a\leq X\leq b$$
$P(a\leq X\leq b) = 1$
$P(X=x) = 0$ for any exact value of x
# Normal / Gaussian Distribution
Continuous probability distribution that is **symmetric about the mean**, depicting that data near the mean is more frequent that data far from it.
+ The symmetry means that events equidistant from the mean have equal probability.
+ Density is highest near the mean, resulting in lower probabilities farther away from it.![a_normal_distribution1](https://media.geeksforgeeks.org/wp-content/uploads/20250606125317822107/a_normal_distribution1.webp)
+ the curve traced by the distribution is in the form of a bell, so it is also called **Bell Curve**.
$$P(X = x) = f(x) = \frac{1}{\sigma \sqrt{2\pi}}e^{\frac{-(x-\mu)^2}{2\sigma^2}}$$
where $x$ = Random variable
$\mu$ = mean
$\sigma$ = standard deviation
+ Properties
	+ Mean median and mode are all equal and at the center of the distribution.
	+ Spread of the distribution is determined by $\sigma$ . About 68% of the data falls within one standard deviation and 95% within two standard deviations.
	+ Not strictly bound to a finite range in general, it can theoretically span from $\infty$ - $-\infty$ 

Normal distribution with $\mu$ = 0 and $\sigma$ = 1 is called **standard normal distribution**  ^a192be
# Poisson Distribution
+ Discrete probability distribution
Probability distribution of a **given number of events occurring in a fixed interval of time or space**, with a known average rate and independent of time since last event.
+ Characteristics
	+ Events occur in a fixed interval, maybe time (no of customers arriving per hour) or space (no of defects per square meter of fabric).
	+ Events occur independently i.e. two events cannot occur at the same instant or location.
	+ The average rate of occurrence ($\lambda$) is constant over the interval.
	+ Cannot have negative values.
	+ For small $\lambda$ ($\lambda < 10$) the distribution is right skewed.
	+ For large $n$ (no of events) and small $p$ , it can be used to approximate a [[Probability Distributions#Binomial Distribution|binomial distribution]]
$$E(X) = \mu = \sigma^2 = \lambda$$
This implies that as the number of events increase, average rate of occurrence increases and so does the variability of the number of occurrences.
Ex: If average no of calls in a call center in an hour($\lambda$) = 2 then range of possible outcomes may be {0, 1, 2, 3, 4} with decreasing probability from the mean. If $\lambda$ = 20 then we see more variability in the no of occurrences like 15, 18, 20, 22, 25 calls.
$$P(X = k) = \frac{e^{-\lambda}\lambda^k}{k!}$$
where $P(X = k)$ = Probability of exactly $k$ events occurring within a fixed interval.
$e^{-\lambda}$ = accounts for randomness of events, ensuring the probability decreases as the number of events deviates from the expected rate.
$\lambda^k$ shows how likely it is for $k$ events to happen based on the average rate $\lambda$.
$k!$ adjusts for how many ways the X events can occur
# Exponential Distribution
+ Continuous probability distribution
Models the time between events occurring, given events occur continuously and independently at a constant average rate($\lambda$).
+ Connection to [[Probability Distributions#Poisson Distribution|poisson distribution]]:
If events occur according to a Poisson process with rate $\lambda$, then the waiting time between events follows a exponential distribution with parameter $\lambda$.
$$P(X=x) = \lambda e^{-\lambda x}\space \forall\space x\geq0, \lambda>0$$
CDF: $$F(x) = 1 - e^{-\lambda x}\space\forall\space x\geq0$$
+ Properties
	+ Memoryless: Knowledge of past events has no effect on future probabilities i.e. an old part is *not more likely* to break down at a particular time than a new part. The part stays as good as new until it suddenly breaks.
	$$P(X> r+t| X > r) = P(X>t)\space\forall\space r\geq0\text{ and } t\geq0 $$
	+ Constant hazard rate: Follows from the memoryless property. The probability of failure in the next small time interval $dt$ , given the event has survived for time $t$ is constant.
	$$P(T\leq t+dt|T>t) = 1-P(T>t+dt|T>t)$$
	$$= 1-P(T>dt) = 1-e^{-\lambda dt}$$
	Substituting $e^{-\lambda dt}$ with the Taylor Series:
	$$ = 1- [1-\lambda dt + \frac{1}{2}\lambda^2 dt^2+....]\approx \lambda  dt$$
	and $\lambda$ is constant.

Ex: Calls arrive at a call center following an exponential distribution with an average rate of 3 calls per hour ($\lambda$ = 3). find the probability of waiting between 10 and 20 minutes for the next call?
Ans: 10 min = $\frac{1}{6}$ , 20 min = $\frac{1}{3}$
$$P(\frac{1}{6} < X < \frac{1}{3}) = \int_{\frac{1}{6}}^{\frac{1}{3}}3e^{-3x}dx = 3\int_{\frac{1}{6}}^{\frac{1}{3}}-e^{-3x}dx \approx 0.2325$$
+ Common pitfalls
	+ Rate vs Mean confusion: 
		+ A common error is using the mean value as the rate parameter $\lambda$
		+ The mean (expected value) = $\frac{1}{\lambda}$ 
		+ Ex: If events occur on average every 2 hours, $\lambda= \frac{1}{2}$ 
	+ Unit mismatch
		+ Rate parameter $\lambda$ must be consistent with time units in data.
		+ If we measure time in hours but specify $\lambda$ in days<sup>-1</sup> , probabilities will be incorrect.  
# Chi-Squared Distribution
Represents the distribution of the sum of squares of $k$ independent [[Probability Distributions#^a192be|standard normal variables]]. If $Z_1,Z_2,...Z_k$ are independent standard normal variables, then $X^2 = Z_1^2+Z_2^2+..+Z_k2$ .
+ Parameterized by degrees of freedom ($k$).
$$f(x,k) = \frac{1}{2^{k/2}\Gamma (k/2)}x^{k/2-1}e^{-x/2},x\geq0$$ where $\Gamma$= gamma function
+ Properties
	+ Defined only for non-negative( $x\geq0$ ) values, since it is sum of squares of standard normal variables.
	+ Shape depends on degrees of freedom ($k$). For small $k$, it is right skewed. As $k\rightarrow\infty$ , becomes a [[Probability Distributions#Normal / Gaussian Distribution|normal distribution]].
	+ Mean = $k$
	+ Variance = $2k$
	+ Skewness = $\sqrt\frac{8}{k}$, As $k\rightarrow\infty$ skewness approaches 0.
	+ Kurtosis = $\frac{12}{k}$
	+ 
# Joint Probability Distribution
+ See [[Probability#Joint Probability|Joint Probability]] first.