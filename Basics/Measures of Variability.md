---
cssclasses:
  - callouts-outlined
---

###### Tags:

---
**Measures of dispersion (variability)** quantify the extent to which data in a distribution is spread out, scattered, or stretched.

## Variance and Standard Deviation

Variance quantifies the spread or dispersion of data points in relation to the mean.
It is the average of the squared differences between each data point and the mean. A high variance indicates that the data points are widely spread out, while a low variance suggests they are clustered closely around the mean.

*Variance is the average of squared deviations from the mean. Standard deviation is just positive square root of variance.*

There are two distinct formulas for variance, depending on whether I'm working with an entire **population** or a **sample** of that population.
I denote variance as $\sigma^{2}$ or $s^{2}$, and standard deviation denoted by $\sigma$ or $s$.

> [!cite] Variance
> For a **population** of $N$, data points $\{x^i \mid x^{i} \in \mathbb{R}^1\}_{i=1}^{N}$ with mean $\mu$, the **population variance**, denoted by $\sigma^{2}$ is defined as $\rightarrow$
>
> $$\sigma^{2} = \frac{\sum_{i=1}^{N}(x^{i} - \mu)^{2}}{N}$$
>
> For a population **sample** of $n$ data points, with mean $\bar{x}$, the **sample variance**, denoted by $s^{2}$, is defined as $\rightarrow$
>
> $$s^{2} = \frac{\sum_{i=1}^{n}(x^{i} - \bar{x})^{2}}{n-1}$$
^def-variance

>[!tip] Why is there a distinction between standard deviation and variance ?
>By taking the square root of the variance, the standard deviation reverses the initial squaring process, returning the measure of spread to the *original units* of the data.
>That's the only distinction between variance and standard deviation. Both convey the same exact information.

#### Why divide by $n-1$ ?

###### Underestimation of Population Variance

I use the sample statistics to estimate the population parameters. And dividing by $n$ would always underestimate the population variance.

>[!abstract] Minimizing variance
>Let $f(v)$ be a function that gives the average of the squared differences from an arbitrary value $v$:
>
>$$f(v) = \frac{1}{n}\sum_{i=1}^{n}(x_{i} − v)^{2}$$
>
>Now I want to find the value of $v$ that minimizes this function.
>
>$$\begin{align}f^{'}(v) & = \frac{d\big(f(v)\big)}{dv} = \frac{1}{n}\sum_{i=1}^{n}2(x_{i}−v)(-1) \\ & = \frac{-2}{n}\sum_{i=1}^{n}(x_{i}−v) \end{align}$$
>
>Find the local minima, equate to zero.
>
>$$\begin{align} \implies & \frac{-2}{n}\sum_{i=1}^{n}(x_{i}−v) = 0 \\  \implies & \sum_{i=1}^{n}(x_{i}−v) = 0 \\ \implies & \sum_{i=1}^{n}(x_{i}) - \sum_{i=1}^{n}(v)  = \sum_{i=1}^{n}(x_{i}) - nv \\ \implies & v =  \frac{\sum_{i=1}^{n}(x_{i})}{n} \end{align}$$
>
>This result is the definition of the sample mean, $\bar{x}$.
>So, the function $f(v)$ has the minimum value at $v=\bar{x}$.

For the population, $f(v)$ would be minimum at $v=\mu$. Thus, $\sum(x_{i}− \bar{x})^2 \leq \sum(x_{i} - \mu)^2$, if I were to calculate the sample variance by dividing by $n$, the value of the variance would be the minimum possible value, and thus it will systematically underestimate the true population variance. Dividing by the smaller number, $n−1$, corrects for this bias and provides a better estimate of the population variance.

This is called **Bessel's Correction**. Dividing by $n-1$, inflates the variance just enough to correct the underestimation of the population variance.

###### Degrees of Freedom

In statistics, **degrees of freedom** refer to the number of values in a final calculation that are truly "free to vary" without violating any constraints imposed by the calculation itself.
When I use a sample to estimate a population parameter, I introduce constraints on my data that reduce the number of independent pieces of information.

Once I know the sample mean and the values of $n−1$ of my data points, the final data point is no longer free to vary.
Since only $n−1$ of the data points can vary independently, there are only $n−1$ independent pieces of information contributing to my estimate of the spread. *Dividing by $n−1$ correctly averages the squared deviations over the number of independent pieces* of information that were used to calculate them, providing an unbiased estimate of the population variance.

##### Why square the difference ?

The primary reason for squaring the difference from the mean is to eliminate negative numbers. Squaring ensures that every deviation contributes a positive value to the total, accurately reflecting the magnitude of the spread regardless of direction.

But I can get rid of negative values by using absolute values as well, why square it ?

- **To Amplify Larger Deviations:** Squaring gives more weight to data points that are farther from the mean. This makes variance sensitive to outliers. A single data point far from the mean will have a significant impact on the final measure of dispersion, which is often a desirable feature when trying to identify volatility or inconsistency in a dataset.

- **For Mathematical Convenience:** Squaring the differences results in a function that is mathematically "well-behaved." The resulting variance formula is easily differentiable. The absolute value function is not as smooth and is more difficult to work with algebraically.

## Co-Variance

**Co-variance describes the joint variability of two random variables.** It tells me how two variables tend to move in relation to each other.
Variance tells me how a single variable varies from its mean. Co-variance tells me how two variables vary from their respective means, in relation to each other.

Co-variance of two variables $X, Y$ is denoted as $\operatorname{cov}(X, Y)$ or sometimes as $\sigma_{XY}$.

>[!cite] Co-variance
>Given a dataset $\mathcal{D} = \{(x^i, y^i) \mid x^i \in \mathbb{R}^1 , y^i \in \mathbb{R}^1\}_{i=1}^{N}$.
>I can refer to the two-dimensions as two random variables $X$ and $Y$. For the $k$-th data-point, the observed value of $X$ is $x^k$ and the observed value of $Y$ is $y^k$.
>
>Co-variance between two random variables  $X, Y$ is the average product of the deviations of each variable from their respective means.
>For a sample of the population, co-variance $s_{XY}$  between variables $X, Y$ is defined as $\rightarrow$
>
>$$s_{XY} = \frac{\sum_{i=1}^{n}(x^{i} - \bar{x})(y^{i} - \bar{y})}{n-1}$$
>
>For a population, co-variance $\sigma_{XY}$ is defined as $\rightarrow$
>
>$$\sigma_{XY}= \frac{\sum_{i=1}^{N}(x^{i} - \mu_{x})(y^{i} - \mu_{y})}{N} $$
^def-covariance

>[!tip] Covariance of $X$ with $X$
>Covariance between the same random variable is nothing but the variance.
>
>$$\sigma_{XX} = \frac{\sum_{i=1}^{N}(x^{i} - \mu_{x})(x^{i} - \mu_{x})}{N} = \frac{\sum_{i=1}^{N}(x^{i} - \mu_{x})^{2}}{N} = \sigma^2_{X} $$

###### Interpreting Co-variance

The most important thing I care about is the **sign of the co-variance** (positive, negative, or zero), which indicates the direction of the relationship.
- **Positive Covariance:** This means that $X, Y$ tend to move in the *same direction*. When one variable is above its average, the other variable also tends to be above its average. Likewise, when one is below average, the other tends to be below average as well.
- **Negative Covariance:** This indicates that $X, Y$ tend to move in *opposite directions*. When one variable is above its average, the other tends to be below its average.
- **Zero Covariance:** This suggests that there is ***no linear relationship*** between $X, Y$. They do not move together in a predictable way.
	- Either the $X, Y$ are *independent* of each other.
	- Or there is no *consistent linear relationship*.

>[!danger] Co-variance gives direction not strength of the relationship
>- Covariance tells me the directionality of the linear relationship between two variables.
>- The magnitude of covariance is largely irrelevant on its own because it is **scale-dependent** and has **no standardized interpretation**.
>- I cannot compare the strength of relationships across different pairs of variables using covariance.
>- The magnitude of the covariance is not used to determine the *strength of a relationship*. It's only used to determine the _direction_.
>- This is where **co-relation** comes in.

## Co-relation

The **correlation coefficient** (often denoted as $r$ for a sample or $\rho$ for a population) is a statistical measure that quantifies both the **strength** and **direction** of the linear relationship between two variables.

Basically, covariance is "normalized" into a unit-less measure called the *correlation coefficient, which is always between $[-1,1]$* and provides a clearer indication of the strength of the relationship.

>[!cite] Co-relation
>The correlation coefficient between two random variable $X, Y$ is calculated by taking the covariance and normalizing it by dividing by the product of the standard deviations of the two variables.
>
>$${\rho_{XY}} = \frac{\operatorname{cov}(X, Y)}{\sigma_{X}\sigma_{Y}}$$
^def-corelation

###### Interpreting Co-relation

- **Positive Correlation ($r > 0$):** This indicates that as one variable increases, the other variable tends to increase. The closer $r$ is to $+1$, the stronger this positive relationship is.
    - **$r = +1$:** A perfect positive linear relationship. All data points fall exactly on a straight line with a positive slope.
- **Negative Correlation ($r < 0$):** This indicates that as one variable increases, the other variable tends to decrease. The closer $r$ is to $-1$, the stronger this negative relationship is.
    - **$r = -1$:** A perfect negative linear relationship. All data points fall exactly on a straight line with a negative slope.
- **No Correlation ($r = 0$):** This means there is no linear relationship between the two variables. This doesn't mean there is no relationship at all, just not a linear one.

>[!danger]
>Both covariance and the standard Pearson correlation coefficient are exclusively concerned with measuring the strength and direction of a _linear_ relationship.
>They are fundamentally *blind to any other kind of relationship*, no matter how strong or predictable it might be. This is one of the most significant limitations of these measures.

## Properties of Variance

>[!cite] Variance
> Variance is the average of squared deviations from the mean. In other words, variance is the expected value of squared deviations from the expected value.$$\operatorname{Var}(X) = E\big[(X - E[X])^2\big]$$
> This can be further expanded as,
> $$  
> \begin{aligned} \operatorname{Var}(X)  
> &= E\big[(X - E[X])^2\big] \\  
> &= E\big[X^2 + (E[X])^2 - 2X E[X] \big] \\  
> &= E[X^2]+ (E[X])^2  - 2E[X]\cdot E[X] \\ \\  
> \operatorname{Var}(X) &= E[X^2] - (E[X])^2 \end{aligned}  
> $$
> This is more often then not, the easiest way to compute variance.
^def-probabilistic-variance

###### Variance is Non-Negative

$$  
\operatorname{Var}(X) \geq 0  
$$
Since every term $(x^i - \mu)^2$ is a square, it is always $\geq 0$, so their average must be $\geq 0$ as well.

###### Variance of a Constant is Zero

$$  
\operatorname{Var}(c) = 0  
$$ Variance equals zero **only** when every data point equals the mean i.e., the variable is a constant. A constant $c$ never deviates from its own mean (since $E[c] = c$), so every squared deviation is zero.

###### Adding a Constant Changes Nothing
$$  
\operatorname{Var}(X + c) = \operatorname{Var}(X)
$$Adding a constant shifts every data point by the same amount, so it shifts the mean by the same amount too. $(x^i + c) - (\mu + c) = x^i - \mu$
The spread of the data is unaffected by shifting the entire distribution.

###### Scaling by a Constant

$$  
\operatorname{Var}(aX) = a^2 \operatorname{Var}(X)
$$Using the definition and $E[aX] = aE[X]$:

$$  
\begin{align} \operatorname{Var}(aX) &= E\big[(aX - aE[X])^2\big] \\
&= E\big[a^2 (X - E[X])^2\big] \\
&= a^2 E\big[(X - E[X])^2\big] \\
&= a^2 \operatorname{Var}(X)
\end{align}
$$     

Scaling stretches deviations by $a$, and since variance squares deviations, the effect is $a^2$.
This is why standard deviation scales linearly:
$$  
\operatorname{SD}(aX) = |a| \operatorname{SD}(X)
$$
###### General Linear Transformation
$$  
\boxed{\begin{align} \operatorname{Var}(aX + b) &= a^2 \operatorname{Var}(X) \\ \\ \operatorname{SD}(aX + b) &= |a| \operatorname{SD}(X) \end{align}}
$$This combines shifting (no effect) and scaling (squared effect).

## Properties of Covariance

>[!cite] Probabilistic definition of Co-variance
>Covariance for two [[Random Variable#^def-random-variable|random variables]] $X, Y$ is defined as the expected value of the product of their deviations from their individual expected values.
>
> $$\operatorname{cov} (X,Y)= E{\Big [(X - E[X])(Y- E[Y])\Big]} $$
>
> This can be further simplified as:
>
> $${\begin{aligned}\operatorname {cov} (X,Y)&=\operatorname {E} \Big[\left(X-\operatorname {E} \left[X\right]\right)\left(Y-\operatorname {E} \left[Y\right]\right)\Big]\\&=\operatorname {E} \Big[XY-X\operatorname {E} \left[Y\right]-\operatorname {E} \left[X\right]Y+\operatorname {E} \left[X\right]\operatorname {E} \left[Y\right]\Big]\\&=\operatorname {E} \left[XY\right]-\operatorname {E} \left[X\right]\operatorname {E} \left[Y\right]-\operatorname {E} \left[X\right]\operatorname {E} \left[Y\right]+\operatorname {E} \left[X\right]\operatorname {E} \left[Y\right]\\ \\ \operatorname{cov}(X, Y) &=\operatorname {E} \left[XY\right]-\operatorname {E} \left[X\right]\operatorname {E} \left[Y\right]\end{aligned}}$$
>
> This identity states that the *covariance of two variables is the expected value of their product minus the product of their expected values.*
^def-probabilistic-covariance

###### Covariance with a Constant is Zero  
$$  
\operatorname{Cov}(X, c) = 0
$$
A constant has no variability at all, so it cannot co-vary with anything.  
###### Shifting by a Constant Changes Nothing  
$$  
\operatorname{Cov}(X + a,\ Y + b) = \operatorname{Cov}(X, Y)
$$
  
The shift $a$ moves both $X$ and its mean $E[X]$ by the same amount, so the deviation is unchanged. The constant vanishes from the deviation, so it has zero effect on covariance. 
###### Scaling by Constants  
  
$$  
\operatorname{Cov}(aX,\ bY) = ab\operatorname{Cov}(X, Y)
$$  This can simply be proven as:
$$  
\begin{align} \operatorname{Cov}(aX, bY) &= E\big[(aX - aE[X])(bY - bE[Y])\big] \\ &= E\big[ab(X-E[X])(Y-E[Y])\big] \\
&=ab\operatorname{Cov}(X,Y) \end{align}
$$
Unlike variance (which gives $a^2$), covariance gives $ab$.  Scaling can flip the **sign** if $a$ or $b$ is negative.  
###### Linear Transformation
$$  
\operatorname{Cov}(aX + b,\ cY + d)
=
ac\operatorname{Cov}(X, Y)
$$
This combines shift in-variance and scaling.
Special case:  
$$  
\operatorname{Cov}(aX + b,\ Y)
=
a\operatorname{Cov}(X, Y)
$$
###### Bi-linearity of Covariance

Covariance is **linear in each of its two arguments separately**. 
$$
\boxed{\operatorname{Cov}(aX + bY,\ Z)
=a\operatorname{Cov}(X, Z)+b\operatorname{Cov}(Y, Z)}
$$
This can be proven as,
$$
\begin{aligned}
\operatorname{Cov}(aX + bY,\ Z)
&=
E\Big[
\big\{(aX+bY) - E[aX+bY]\big\}\big\{Z - E[Z]\big\}
\Big]
\end{aligned}
$$

Since $E[aX + bY] = aE[X] + bE[Y]$, I can simplify,
$$
\begin{aligned}
\operatorname{Cov}(aX + bY,\ Z)
&= E\Big[\big\{(aX+bY) - E[aX+bY]\big\}\big\{Z - E[Z]\big\}\Big] \\
&= E\Big[\big\{aX+bY - aE[X] -bE[Y]\big\}\big\{Z - E[Z]\big\}\Big] \\
&= E\Big[\big\{a(X - E[X]) + b(Y - E[Y])\big\}\big\{Z - E[Z]\big\}\Big]\\
&= E\Big[a(X-E[X])(Z-E[Z])+b(Y-E[Y])(Z-E[Z])\Big] \\
&= a\underbrace{E\big[(X-E[X])(Z-E[Z])\big]}_{\operatorname{Cov}(X,Z)}+b\underbrace{E\big[(Y-E[Y])(Z-E[Z])\big]}_{\operatorname{Cov}(Y,Z)} \\ \\
\operatorname{Cov}(aX + bY,\ Z) &=a\operatorname{Cov}(X, Z)+b\operatorname{Cov}(Y, Z)
\end{aligned}
$$
Similarly, I can prove,
$$
\boxed{\operatorname{Cov}(Z,\ aX + bY)
=
a\operatorname{Cov}(Z, X)
+
b\operatorname{Cov}(Z, Y)}
$$
**Covariance can be treated like multiplication distributed over a sum**.
$$
\begin{align} \operatorname{Cov}(X+Y,\ X+Y)
&= \operatorname{Cov}(X,\ X+Y) + \operatorname{Cov}(Y,\ X+Y)
\\ &= \operatorname{Cov}(X,X)+\operatorname{Cov}(X,Y) + \operatorname{Cov}(Y,X)+\operatorname{Cov}(Y,Y) \end{align}
$$

###### Variance of a Sum and Difference
$$
\boxed{
\begin{align} \operatorname{Var}(X + Y)
&=\operatorname{Var}(X)+\operatorname{Var}(Y)+2\operatorname{Cov}(X, Y)
\\ \\\operatorname{Var}(X - Y) &=\operatorname{Var}(X)+ \operatorname{Var}(Y)-2\operatorname{Cov}(X, Y)\end{align}}
$$
This can be proven using the property $\operatorname{Cov}(X, X) = \operatorname{Var}(X)$ as,
$$  
\begin{aligned}
\operatorname{Var}(X+Y)
&= \operatorname{Cov}\big((X+Y),\ (X+Y)\big) \\
&= \operatorname{Cov}(X,X)
+ \operatorname{Cov}(X,Y)
+ \operatorname{Cov}(Y,X)
+ \operatorname{Cov}(Y,Y) \\
&= \operatorname{Var}(X)
+ \operatorname{Var}(Y)
+ 2\operatorname{Cov}(X,Y)
\end{aligned}
$$
In general for $n$ random variables, 
$$\boxed{\operatorname{Var}\left(\sum_{i=1}^{n}X_i\right)= \sum_{i=1}^{n}\sum_{j=1}^{n}\operatorname{Cov}(X_i, X_j)}$$
Also, in general,
$$\boxed{  
\operatorname{Var}(aX + bY)
=
a^2\operatorname{Var}(X)
+
b^2\operatorname{Var}(Y)
+
2ab\operatorname{Cov}(X, Y)}
$$This combines the scaling rule applied to each variable and the sum rule, the cross term $2ab \operatorname{Cov}(X,Y)$ accounts for the interaction between the two variables.

###### Independent Variables

If $X$ and $Y$ are **independent**, then,
$$  
\begin{align} \operatorname{Cov}(X,Y) &= 0 \\ \\
\operatorname{Var}(X + Y) &=\operatorname{Var}(X)+\operatorname{Var}(Y) \\ \\ \operatorname{Var}(aX + bY) &=a^2\operatorname{Var}(X)+b^2\operatorname{Var}(Y) \end{align}
$$

## Summary

| Property                     | Formula                                                                                                        |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Non-negativity               | $\operatorname{Var}(X) \geq 0$                                                                                 |
| Constant                     | $\operatorname{Var}(c) = 0$                                                                                    |
| Shift by constant            | $\operatorname{Var}(X+c) = \operatorname{Var}(X)$                                                              |
| Scale by constant            | $\operatorname{Var}(aX) = a^2 \operatorname{Var}(X)$                                                           |
| Linear transform             | $\operatorname{Var}(aX+b) = a^2 \operatorname{Var}(X)$                                                         |
| Computational formula        | $\operatorname{Var}(X) = E[X^2] - (E[X])^2$                                                                    |
| Sum of two variables         | $\operatorname{Var}(X+Y) = \operatorname{Var}(X) + \operatorname{Var}(Y) + 2\operatorname{Cov}(X,Y)$           |
| Difference                   | $\operatorname{Var}(X-Y) = \operatorname{Var}(X) + \operatorname{Var}(Y) - 2\operatorname{Cov}(X,Y)$           |
| Linear combination           | $\operatorname{Var}(aX+bY) = a^2\operatorname{Var}(X) + b^2\operatorname{Var}(Y) + 2ab\operatorname{Cov}(X,Y)$ |
| General sum of $n$ variables | $\operatorname{Var}\!\left(\sum_i X_i\right) = \sum_i \sum_j \operatorname{Cov}(X_i, X_j)$                     |
| Independent variables        | $\operatorname{Var}(X+Y) = \operatorname{Var}(X) + \operatorname{Var}(Y)$                                      |

| Property         | Formula                                                                              |
| ---------------- | ------------------------------------------------------------------------------------ |
| Constant         | $\operatorname{Cov}(X, c) = 0$                                                       |
| Shift            | $\operatorname{Cov}(X+a,\ Y+b) = \operatorname{Cov}(X,Y)$                            |
| Scale            | $\operatorname{Cov}(aX,\ bY) = ab\operatorname{Cov}(X,Y)$                            |
| Linear transform | $\operatorname{Cov}(aX+b,\ cY+d) = ac\operatorname{Cov}(X,Y)$                        |
| Symmetry         | $\operatorname{Cov}(X,Y) = \operatorname{Cov}(Y,X)$                                  |
| Self-covariance  | $\operatorname{Cov}(X,X) = \operatorname{Var}(X)$                                    |
| Bilinearity      | $\operatorname{Cov}(aX+bY, Z) = a\operatorname{Cov}(X,Z) + b\operatorname{Cov}(Y,Z)$ |
| Independence     | $X \perp Y \Rightarrow \operatorname{Cov}(X,Y) = 0$                                  |
