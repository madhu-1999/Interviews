#ml #data-science #math
```table-of-contents
title: 
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
[[Permutations and Combinations]]
# Type of Events
## Independent Event
+ Outcome of one event does not affect outcome of other event.
+ Ex: dice roll and coin flip are independent events.
$$P(A\cap B) = P(A) * P(B)$$
$$ P(A|B) = P(A)$$
## Mutually exclusive Event
+ Two events cannot happen simultaneously.
+ Ex: Getting both heads and tails on one coin flip.
$$ P(A\cap B) = 0$$
$$ P(A|B) = 0$$
## Mutually exhaustive Events
+ Set of events which covers all possible outcomes, meaning one of them must occur.
+ Note: Two events in the set can occur simultaneously.
Let $A = \{A_1,...A_k\}$ be a set of mutually exhaustive events, then:
$$A_i \cup A_j = \phi\space\forall\space i\neq j$$
$$A_1\cup A_2\cup ...\cup A_k = A$$
$$P(A_1) + P(A_2) +....+P(A_k) = 1$$
## Dependent Event
+ Outcome of events depends on each other. Conditional probability.
# Random Variable
+ Function that assigns a numerical value to each outcome in the sample space of a random experiment.
Ex: 2 Coin flips , Sample space = {H, T} 
X is random variable denoting outcome of experiment
P(X) is the function assigning numerical value to each outcome
$$P(X = HH) = \frac{1}{4}$$
+ A random variable always samples from a distribution.
	+ Distribution: A mathematical description of how likely different values are to occur. It tells us the probability of each outcome.
	+ "samples from": Random variable takes on values specified by the distribution.
## Discrete Random Variable
+ The random variable takes on a finite or countably infinite i.e. discrete value.
+ Associated with Probability Mass Function (PMF).
### **PMF** 
+ Probability that a random variable takes a value **exactly equal to x**.
If X is a discrete random variable and the PMF of X is P(X) then:
$$0 \leq P(x_i)\leq1$$
$$\sum p(x_i) = 1,\text{where } P(x_i) \text{ is probability of an outcome}$$
Ex: Dice roll of a six-sided, fair die. 
Sample space = {1, 2, 3, 4 ,5, 6}
Each outcome is equally likely i.e
$$0\leq P(x_i) = \frac{1}{6} \leq 1$$
and $$\sum P(x_i) = \frac{1}{6}\times 6 = 1$$
![](https://analystprep.com/cfa-level-1-exam/wp-content/uploads/2021/09/cfa-level-1-roll-dice.jpg)
Random variable X such that X<sub>i</sub> = i, where i = 1, 2, 3...k
$$P(X=x) = \frac{1}{k}$$
### **CDF**
+ Probability that a random variable X, will take a value **equal to or less than x**.
+ Only valid for discrete distributions since PDF is already calculated over a range.
$$F_X(a) = P(X \leq a)$$
+ If minimum value of a discrete random variable is $b$ , then $$F_X(b) = P(X\leq b) = P(X = b)$$
	+ For any value $y < a$ , $F_X(y)  = 0$ 
+ If maximum value of discrete random variable is $c$, then $$F_X(c) = P(X\leq c) = 1$$
	+ For any value $y > c$, $F_X(y) = 1$ 
## Continuous Random Variable
+ Takes on infinite number of values
+ Associated with Probability Density Function (PDF).
### **PDF**
If X is a continuous random variable, P(x < X < x + dx) = f(x)dx then,
$$0\leq f(x) \leq 1 \space\forall \space x$$
$$\int f(x)dx = 1\space\forall \space x$$
Ex: If PDF is given by  $f(x) = kx^3;$ then, in general
$$
P(X) =\int f(x)dx = \int kx^3dx = \frac{kx^4}{4}
$$
since it is continuous, probability is found over a range: $$P(1 < X  < 2) = k[\frac{2^4 - 1^4}{4}] = k\frac{15}{4}$$
and the value of k can be found, given f(x) spans the range $0\leq x\leq 3$: 
$$\int_{0}^{3} f(x) dx = 1 $$
$$ k\frac{3^4 - 0^4}{4} = 1 = k\frac{81}{4}$$
$$k =\frac{4}{81}$$

+ f(x) graphically depict the distribution of values over the continuous range.
+ ***Note***: $P(X = x) = 0\space\forall\space x$ . For a continuous random variable, area under the graph gives probability i.e. always over a range.
## Expectation E(X)
+ For a random variable X, E(X) represents the average value we predict will be observed if we repeatedly sampled from the random variable's distribution. 
+ It is a theoretical prediction of average in the long run while **mean** is the actual average of a set of data.
+ Discrete Random Variable:
$$E(X) = \sum P(x_i)\times x_i$$
Ex: Coin toss: Let X be the random variable.
X = 0 denotes head and X = 1 denotes tail
then $$E(X) = P(X=0)\times X_0 + P(X=1)\times X_1 = 0.5\times 0 + 0.5\times 1 = 0.5$$
This means that over many coin flips, the average outcome would be close to 0.5 (half head, half tails).
+ Continuous Random Variable:
$$E(X) = \int xf(x) dx$$ over the entire range of X
+ If $E(X)= \infty$ , then we say that $E(X)$ does not exist. i..e t**he expectation of a random variable $X$ may or may not exist**.  
## Variance of Random Variable
$$\sigma^2 = E(X^2) - (E(X))^2$$
$$ =\sum P(x_i)\times x_i^2 \space -(\sum P(x_i)\times x_i)^2$$
## Uses
- They are used to study the average behavior of algorithms that use random steps, such as QuickSort.
- In machine learning, they help describe input data, predictions, and errors.
- Simulations use them to model events like customer arrivals or data flow in a network.
- In cybersecurity, random variables help create strong, unpredictable keys.
- They are also useful in checking how well data structures like hash tables perform.
- In networking, they help predict delays and traffic.
- Even in games and animation, random variables create random actions and natural-looking effects.
# Joint Probability
+ Probability of event A and event B occurring.
+ Ex: Probability that card is a four and red. (simultaneous occurrence)
+ Ex: Probability of drawing an ace and then a king (non-simultaneous occurrence)
## Discrete Random Variables
+ PMF is given by $f(x,y) = P(X=x, Y=y)$ or denoted by $f_{X,Y}$ 
+ For any such PMF, three things are true:
	+ $0\leq f(x,y)\leq 1\space\forall\space(x,y)$ 
	+ $\sum f(x,y) = 1$ 
	+ For any event $A\subset S$ ($S$ = sample space): $$P[(X,Y)\in A] = \sum_{(x,y)\in A}f(x,y)$$
Ex: Roll two, fair, six-sided die.
$X$ = outcome of first die
$Y$ = outcome of second die
$f(x,y) = \frac{1}{36}$ 
$A$ = Sum of dice is 7= $\{(1,6),(6,1),(2,5), (5,2),(3,4),(4,3)\}$
$$P[(X,Y)\in A] = \sum_{(x,y)\in A}f(x,y)$$
$$=f(1,6) + f(6,1) + f(2,5) + f(5,2) + f(3,4) + f(4,3)$$
$$=\frac{6}{36} = \frac{1}{6}$$
## Continuous Random Variables
+ PDF is given by $f(x,y)$.
+ For any PDF, three things are true:
	+ $f(x,y) \geq 0\space\forall\space (x,y)$
	+ $\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}f(x,y)dxdy = 1$
	+ For any event $A\subset S$ ($S$ = sample space):$$P[(X,Y)\in A] = \int\int f(x,y)dxdy$$
## 
Joint CDF for both discrete and continuous is given by: $F_{X,Y}(x,y) = P(X\leq x,Y\leq y)$ 
# Marginal Probability
+ Unconditional probability of an event $A$ occurring. 
+ Derived from [[#Joint Probability]] and represents likelihood of event happening in isolation.
+ Ex: Probability of drawing a red card.
## Discrete Random Variables
$$P(X=x) = \sum_yP(X=x,Y=y)$$
$$P(Y=y) = \sum_xP(X=x,Y=y)$$
## Continuous Random Variables
$$f_X(x) = \int_{-\infty}^{\infty} f_{X,Y}(x,y) dy$$
$$f_Y(y) = \int_{-\infty}^\infty f_{X,Y}(x,y)dx$$
## 
***NOTE***: $\sum f_X(x) = \sum f_Y(y) = 1$
# Conditional Probability
$$
P(A|B) = \frac{P(A\cap B)}{P(B)} 
\text{  where } P(B) > 0
$$
$$
P(A|B) = \text{Proability of event A occuring given that event B has occured}
$$
$$
P(A\cap B) = \text{Probabilty of both events A and B occuring together}
$$
Ex: What is the probability of drawing a king if card drawn is a face card?
$$ A \rightarrow \text{drawing a king}$$
$$ B\rightarrow\text{drawing a face card}$$
$$P(A\cap B) = \frac{4}{52}$$
$$ P(B) = \frac{12}{52}$$
$$P(A|B) = \frac{P(A\cap B)}{P(B)} = \frac{\frac{4}{52}}{\frac{12}{52}} = \frac{4}{12}$$
## Key properties
+  Independent events:
$$ P(A|B) = P(A)$$
+ Complement Rule:
$$ P(A|B) + P(\bar{A}|B) = 1$$
+ Chain rule:
$$P(A\cap B\cap C) = P(A|B\cap C)\times P(B|C)\times P(C)$$
	Ex: 
	1. Drawing a king (K)
	2. From remaining cards, drawing a queen (Q)
	3. From remaining cards, drawing an ace (A)
	$$P(K\cap Q\cap A) = P(A|K\cap Q)\times P(Q\cap K)\times P(K) = \frac{4}{50}\times \frac{4}{51}\times \frac{4}{52}$$

# Bayes Theorem
$$P(A|B) = \frac{P(B|A)\times P(A)}{P(B)}$$
P(A): Prior probability - initial belief about A before seeing evidence B.
P(B|A): Likelihood - how probable evidence B is if A is true
P(B): Total probability of observing evidence B
P(A|B): Posterior probability - updated belief about A after seeing evidence B

Ex: Consider a medical condition affecting 2% of the population. A new diagnostic test has:

- 95% sensitivity: P(positive test | has condition) = 0.95
- 90% specificity: P(negative test | no condition) = 0.90
Let's solve this step by step:

1. Prior probability (before testing): P(condition) = 0.02 (2% population rate)
2. Likelihood of a positive test given the condition: P(positive|condition) = 0.95 (test sensitivity)
3. Total probability of a positive test: P(positive) = P(positive|condition) × P(condition) + P(positive|no condition) × P(no condition) = 0.95 × 0.02 + 0.10 × 0.98 = 0.019 + 0.098 = 0.117
4. Posterior probability (using Bayes' Theorem): P(condition|positive) = [0.95 × 0.02] / 0.117 ≈ 0.162 or 16.2%
## The power of Bayesian updating

Bayes' Theorem shines when we receive multiple pieces of evidence. Each calculation's posterior probability becomes the prior probability for the next update. For example, if our patient gets a second positive test:

1. New prior: P(condition) = 0.162 (from our first calculation)
2. Likelihood: P(positive|condition) = 0.95
3. Calculate new posterior...
## Alternative formula
Let $A_1....A_k$ be mutually exhaustive and exclusive events with prior probabilities $P(A_i),i=1...k$ .
Let $B$ be any event with $P(B)$ > 0.
Then the posterior probability of $A_i$ given $B$ has occurred is: $$P(A_i|B) = \frac{P(B|A_i)P(A_i)}{\sum_{j=1}^kP(B|A_j)P(A_j)}$$
Ex: $A_1$ = “spam,” $A_2$ = “low priority” and $A_3$ = “high priority.” 
$P(A_1)$ = .7, $P(A_2)$ = .2 and $P(A_3)$ = .1. Of course, .7 + .2 + .1 = 1.
Let $B$ be the event that the email contains the word “free.” 
$P(B|A_1)$ = .9, $P(B|A_2)$ = .01, $P(B|A_1)$ = .01. (Note: .9 + .01 + .01 (= 1.) 
I receive an email with the word “free.” What is the probability that it is spam? $$P(A_1|B) = \frac{P(B|A_1)P(A_1)}{P(B|A_1)P(A_1) + P(B|A_2)P(A_2) + P(B|A_3)P(A_3)}=.995$$
## Common Pitfalls and Misinterpretations

When working with conditional probability, three main misconceptions often lead to incorrect conclusions.

- **The inverse fallacy**, also known as the confusion of the inverse, occurs when we mix up P(A|B) with P(B|A). We saw this in our medical testing example - the probability of having a condition given a positive test differs from the probability of testing positive given you have the condition.
- **Base rate neglect** happens when we focus on conditional probabilities while ignoring the underlying probability of an event. For instance, if a rare disease affects only 1 in 10,000 people, even a highly accurate test might yield mostly false positives simply because the disease is so uncommon.
- The third pitfall involves **incorrectly weighing evidence when updating probabilities**. People tend to overweight recent or dramatic evidence while underweighting stable background information. In financial trading, for example, investors might overreact to recent market news while ignoring long-term market probabilities.
# Total Probability
If $A_1, A_2, ...A_n$ are mutually exclusive and exhaustive events i.e. they cover all possible outcomes and do not overlap, and $B$ is an event of  interest, such that $$ 
B = (B\cap A_1) \cup (B\cap A_2)\cup ...(B\cap A_n)
$$then
$$
P(B) = \sum P(B\cap A_i) = \sum P(B|A_i)\times P(A_i)
$$
Ex: Bag $A_1$ has 2 R and 3 G balls.
Bag $A_2$ has 3 R and 2 G balls.
Bag $A_3$ has 1 R and 4 G balls.
A ball is randomly selected from a random bag. What is the probability that the ball is red?

Let A be event of choosing a ball from a random bag and B be the event of ball being red.
$$
P(B) = \frac{1}{3} \times \frac{2}{5} + \frac{1}{3}\times \frac{3}{5}+\frac{1}{3}\times \frac{1}{5} = \frac{2}{5}
$$
$$\text{where}, P(A) = \frac{1}{3}$$
