---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Parametric v/s Non-parametric Estimation

###### Estimator and Estimate

>[!cite] Estimator
>In statistical inference, an **estimator** is a function (a rule), whereas an **estimate** is the specific numerical value resulting from that function.
>The quantity being estimated is called the **estimand**.
>
>Formally, let $X = \{X_{1},X_{2},…,X_{n}\}$ be a random sample from a population distribution characterized by a parameter $\boldsymbol{\theta} \in \boldsymbol{\Theta}$ (which may be a vector) belonging to a parameter space $\boldsymbol{\Theta}$.
>
>
>An **estimator** $\boldsymbol{\hat{\theta}}$ is a [[Measure Theory#^def-measurable-function|measurable function]] $T: X \rightarrow \boldsymbol{\Theta}$ that maps the sample space $X$ (the set of all possible data outcomes) to the parameter space $\boldsymbol{\Theta}$. Where, $$\boldsymbol{\hat{\theta}} = T(X_{1},X_{2},…,X_{n})$$
>
>When I plug actual observed data $x=\{x_{1},x_{2},…,x_{n}\}$ into this function, the result $t=T(x)$ is called the **estimate**.
^def-estimator

An estimator is a rule (or a function) for computing an estimate of a certain quantity from observed data. *Sample Mean is an estimate of Population Mean.*
- The Estimator: $\bar{X} = \frac{1}{n}\sum_{i=1}^{n}X_i$ (This is a random variable; a rule).
- The Estimate: $\bar{x} = 0.5$ (This is a fixed number derived from specific data)

###### Parametric Estimation

> [!cite] Parametric Estimation
> Parametric estimation assumes that the underlying probability distribution $P$ generating the data belongs to a known family of distributions $\mathcal{P}$, which can be entirely described by a finite, fixed number of parameters.
> Mathematically, I assume the model takes the form:
> $$\mathcal{P} = \{f(x;\boldsymbol{\theta})\ | \ \boldsymbol{\theta} \in \boldsymbol{\Theta} \subseteq \mathbb{R}^k\}$$
> where $k$ is a fixed integer (the number of parameters) that does not change regardless of the sample size $n$.
^def-parametric-estimation

>[!tip] theta vs Theta
>$\boldsymbol{\theta}$ represents the **true, fixed, but unknown constant(s)** that define the underlying probability distribution $P_{\boldsymbol{\theta}}$ of the population from which my data is drawn.
>- $\boldsymbol{\theta}$ is a vector of dimension $k$ (where $k \ge 1$).
>- It indexes the probability density function (PDF) or probability mass function (PMF). I write $f(x; \boldsymbol{\theta})$ or $P_\boldsymbol{\theta}(x)$ to show that the shape of the distribution $f$ is entirely controlled by the value of $\boldsymbol{\theta}$.
>
>$\boldsymbol{\Theta}$ is the **set of all possible valid values** that the parameter $\boldsymbol{\theta}$ can take.
>- $\boldsymbol{\Theta} \subseteq \mathbb{R}^k$ is the domain of valid parameters.
>- It defines the boundaries of my model. My estimator $\hat{\theta}$ must map data into this space (or at least be evaluated against values within this space).


*I assume the data follows a specific, known distribution (like a Normal/Gaussian distribution). I assume that I know the shape of the data, my only job is to calculate the fixed "parameters" that define that shape.*

For a Gaussian, I only need to estimate two numbers: the Mean ($\mu$) and Variance ($\sigma^2$). Once I have those, I throw away the original data because the Gaussian formula tells you everything.

###### Non-Parametric Estimation

Non-parametric estimation makes no assumption that the data distribution $P$ belongs to a parametric family defined by a finite vector $\boldsymbol{\theta}$. *The parameter space $\boldsymbol{\Theta}$ is infinite-dimensional.*

Instead of estimating a vector $\boldsymbol{\theta} \in \mathbb{R}^k$, I'm more often estimating the function $f$ itself (the probability density function) or a functional of it. The number of effective parameters is not fixed; it typically grows with the sample size $n$.

I make no strong assumptions about the probability distribution (often called "distribution-free"). *I assume the data is the distribution*. Instead of compressing the data into two or three numbers, the model often keeps all the data points to define the shape.

I do not assume the data follows a specific math formula like a line or a bell curve. Instead, the "parameter" I'm trying to find is the _entire shape_ of the distribution itself. Because describing a free-form shape requires infinite detail (or at least as many points as you have data), the complexity of the model scales up as I collect more data.

| Feature            | Parametric Estimation                                                                                                    | Non-Parametric Estimation                                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Parameter Space    | **Fixed.** The number of parameters (e.g., $\beta_0, \beta_1$) is constant, regardless of how much data ($n$) I collect. | **Infinite (or Growing).** The effective number of parameters grows with the sample size $n$. Examples include $k$-Nearest Neighbors or Histograms. |
| Computational Cost | **Low.** Fast to train and predict because the model is just a simple formula.                                           | **High.** Can be slow because the model may need to process the entire dataset for every prediction.                                                |
| Data Efficiency    | **High.** Needs less data to work well, if your assumption is right.                                                     | **Low.** Needs massive amounts of data to accurately map the complex structure.                                                                     |

#### Density Estimation

**Density Estimation** is just one specific type of estimation. **Estimation** is the big umbrella term for inferring any unknown value from data.
The reason we mostly focus on Density Estimation is that it's the easiest way to visualize the difference (smooth bell curve vs. jagged bumpy line). However, the parametric/non-parametric distinction applies to almost every problem in statistics, not just finding shapes of distributions.

Density estimation is a statistical method used to estimate an un-observable, **underlying probability density function (PDF) from a set of observed data.** The goal is to model the probability distribution from which a sample of data has been drawn.

###### Parametric Density Estimation

Parametric density estimation **assumes** that the data is drawn from a *known probability distribution*, such as a normal (Gaussian) distribution or a Poisson Distribution. The process involves *estimating the parameters that define this assumed distribution.*

For example, if the data is assumed to follow a normal distribution, the task is to estimate its two parameters: the mean ($\mu$) and the standard deviation ($\sigma$), using the sample data. Once these parameters are estimated, the probability density function is fully defined. This method is called "parametric" because the final density curve depends entirely on the values of these estimated parameters.

###### Non-Parametric Density Estimation

Non-parametric density estimation makes **no assumptions** about the underlying probability distribution of the data. *Instead of estimating a few parameters for a predefined curve, this method constructs an estimate of the density function* directly from the data points themselves.

This approach is more flexible, as it can capture complex distributions that do not fit a standard parametric model. Common examples of non-parametric methods include:
- **Histograms:** Data is grouped into bins, and the density is represented by the height of each bin.
- **Kernel Density Estimation (KDE):** A smooth curve is generated by placing a kernel (a "bump" function) over each data point and summing them up. This is a powerful, data-driven method for modeling the distribution.

## Bias and Variance

> [!cite] Bias
> Let $\{X_{1},X_{2},…,X_{n}\}$ be a random sample from a population distribution $P_{\boldsymbol{\theta}}$ parameterized by $\boldsymbol{\theta} \in \boldsymbol{\Theta}$. Let $\hat{\theta} = T(X_{1},X_{2},…,X_{n})$ be an estimator of $\boldsymbol{\theta}$ (i.e. $\hat{\theta}$ is the function that estimates $\boldsymbol{\theta}$).
>
> The **bias of an estimator** $\hat{\theta}$ is the difference between the expected value of the estimator and the true parameter value.
> $$\text{Bias}_{\boldsymbol{\theta}}(\hat{\theta}) = \mathbb{E}_{\boldsymbol{\theta}}[ \hat{\theta}] - \boldsymbol{\theta}$$
> Where:
> - $\mathbb{E}_{\boldsymbol{\theta}}[\hat{\theta}]$ denotes the expected value of the estimator with respect to the distribution $P_{\boldsymbol{\theta}}$.
<<<<<<< HEAD
> - If $\text{Bias}_{\boldsymbol{\theta}}(\hat{\theta}) = 0$ for all $\boldsymbol{\theta} \in \boldsymbol{\Theta}$, the estimator is called **unbiased**.
=======
> - If $\text{Bias}_{\boldsymbol{\theta}}(\hat{\theta}) = 0$  for all $\boldsymbol{\theta} \in \boldsymbol{\Theta}$, the estimator is called **unbiased**.
>>>>>>> 4b5aa16882932006cf4d2d597898f7001118d633
> - If $\text{Bias}_{\boldsymbol{\theta}}(\hat{\theta}) > 0$, the estimator tends to overestimate $\boldsymbol{\theta}$.
> - If $\text{Bias}_{\boldsymbol{\theta}}(\hat{\theta}) < 0$, the estimator tends to underestimate $\boldsymbol{\theta}$.
> 
> Bias measures the **average error** of the estimator. It asks: _"If I repeated this experiment infinite times and averaged the estimates, how far of would I be from the actual estimand?"_
>
>The Bias **at a specific valid setting $\boldsymbol{\theta}$** (from the space $\boldsymbol{\Theta}$) is the difference between the average guess of my estimator $\mathbb{E}_{\boldsymbol{\theta}}[\hat{\theta}]$ (calculated using the probability rules defined by that specific $\boldsymbol{\theta}$) and the specific $\boldsymbol{\theta}$ itself.
^def-estimator-bias

> [!cite] Estimator Variance
> Let $\{X_{1},X_{2},…,X_{n}\}$ be a random sample from a population distribution $P_{\boldsymbol{\theta}}$ parameterized by $\boldsymbol{\theta} \in \boldsymbol{\Theta}$. Let $\hat{\theta} = T(X_{1},X_{2},…,X_{n})$ be an estimator of $\boldsymbol{\theta}$ (i.e. $\hat{\theta}$ is the function that estimates $\boldsymbol{\theta}$).
>
> The variance of an estimator $\hat{\theta}$ is the expected squared deviation of the estimator from its own expected value.
> $$\text{Var}_{\boldsymbol{\theta}}(\hat{\theta}) = \mathbb{E}_{\boldsymbol{\theta}}\Big[(\hat{\theta} - \mathbb{E}_{\boldsymbol{\theta}}[\hat{\theta}])^2\Big]$$
> Alternatively, using the property $\text{Var}(X)=\mathbb{E}[X^2]−(\mathbb{E}[X])^2$: $$\text{Var}_{\boldsymbol{\theta}}(\hat{\theta}) = \mathbb{E}_{\boldsymbol{\theta}}[\hat{\theta}^2] - (\mathbb{E}_{\boldsymbol{\theta}}[\hat{\theta}])^2$$
>
>Variance measures the **consistency** of the estimator. It asks: _"If I repeated this experiment with a new set of data, how much would my estimate change?"_
>- It calculates how far individual estimates $\hat{\theta}$ tend to fall from their own average $\mathbb{E}[\hat{\theta}]$.
> - $\text{Var}_{\boldsymbol{\theta}}(\hat{\theta})$ measures the precision or stability of the estimator.
> - It is strictly non-negative: $\text{Var}_{\boldsymbol{\theta}}(\hat{\theta}) \geq 0$
> - Crucially, variance **does not care about the true value $\boldsymbol{\theta}$**. It only cares about how scattered the estimates are relative to each other.
^def-estimator-variance

Given an estimator $\hat{\theta}$, one can always ask the questions:
- "How biased is my estimator: ($\text{Bias}(\hat{\theta}$)?"
- "How my variance does my estimator has: $\text{Var}(\hat{\theta})$?"

## Sample Mean and Sample Variance as Estimators

I have some observed data (my sample), and I use the sample mean ($\hat{\mu}$) and sample variance ($\hat{\sigma}^2$) to estimate the true population mean ($\mu$) and population variance ($\sigma^2$) respectively. I have my estimands and I have my estimators. *So, how good are these estimators?*

#### Independent and Identically Distributed Data

Let $X_1, X_2, \dots, X_n$ be a sequence of random variables representing a sample of size $n$.
I assume these variables are **Independent and Identically Distributed (I.I.D.)**.
This implies two things for all $i, j$:
1.  **Identical Distribution:** They all share the same population mean $\mu$ $$\begin{align} \boldsymbol{\forall} \ i, \ \  \mathbb{E}[X_i] &= \mathbb{E}[X] = \mu \\ \\ \boldsymbol{\forall} \ i, \ \ \mathbb{E}[X_i^2] &= \mathbb{E}[X^2] \end{align}$$ And the same population variance $\sigma^2$, $$\begin{align} \text{Var}(X_i) &=  \sigma^2 \\ \\ \implies   \sigma^2&= \mathbb{E}[X_i^2] - (\mathbb{E}[X_i])^2 \\ \sigma^2 &= \mathbb{E}[X^2] - \mu^2   \end{align}$$
2.  **Independence:** The outcome of one does not influence the other. Covariance is zero.$$ \begin{align} \mathbb{E}[X_i X_j] = \mathbb{E}[X_i]\mathbb{E}[X_j] \quad \text{for } i \neq j \\ \\ \text{Cov}(X_i, X_j) = 0 \quad \text{for } i \neq j  \end{align}$$

#### Sample Mean as an Estimator

The goal is to prove that the  ($\hat{\mu}$) is a perfect estimate of the true population mean ($\mu$) on average.
1. Prove that the Sample Mean estimator $\hat{\mu} = \frac{1}{n} \sum X_i$ is **Unbiased**.
2. Prove that the **variance** of the Sample Mean **approaches zero**.

The bias of an estimator $\hat{\theta}$ for a parameter $\theta$ is given by, $\text{Bias}(\hat{\theta})= \mathbb{E}[\hat{\theta}] - {\theta}$. Here, $\hat{\mu}$ is the estimator for the parameter $\mu$. So,
$$\text{Bias}(\hat{\mu})= \mathbb{E}[\hat{\mu}] - \mu$$

The variance of an estimator $\hat{\theta}$ is given by, $\text{Var}(\hat{\theta}) = \mathbb{E}\Big[ (\hat{\theta} - \mathbb{E}[\hat{\theta}])^2 \Big] = \mathbb{E}[\hat{\theta}^2] - (\mathbb{E}[\hat{\theta}])^2$. Here,$\hat{\mu}$ is my estimator. So,
$$\text{Var}(\hat{\mu}) = \mathbb{E}\Big[ (\hat{\mu} - \mathbb{E}[\hat{\mu}])^2 \Big] = \mathbb{E}[\hat{\mu}^2] - (\mathbb{E}[\hat{\mu}])^2$$

###### Proof 1: Unbiasedness of Sample Mean

The expectation of $\hat{\mu}$ is,
$$ \begin{align} \mathbb{E}[\hat{\mu}] &= \mathbb{E}\left[ \frac{1}{n} \sum_{i=1}^n X_i \right] \\ \\  &= \frac{1}{n} \sum_{i=1}^n \mathbb{E}[X_i]   \end{align}$$

Since all $X_i$ are *identically distributed* with mean $\mu$, i.e. $\mathbb{E}[X_i] = \mu$:
$$\mathbb{E}[\hat{\mu}]  = \frac{1}{n} \sum_{i=1}^n \mu = \frac{1}{n} (n\mu) = \mu $$

Finally,
$$\begin{align} \text{Bias}(\hat{\mu})= \mathbb{E}[\hat{\mu}] - \mu \\ \\ \text{Bias}(\hat{\mu}) = \mu - \mu = 0 \end{align}$$
Conclusion: **The Sample Mean $\hat{\mu}$ is an Unbiased Estimator of Population Mean $\mu$.**

###### Proof 2: Variance of Sample Mean

The expectation of $\hat{\mu}$ is $\mathbb{E}[\hat{\mu}] = \mu$. I can find $\mathbb{E}[\hat{\mu}^2]$ as,

$$\begin{align} \mathbb{E}[\hat{\mu}^2] = \mathbb{E}[\hat{\mu}. \hat{\mu}] &= \mathbb{E}\left[ \left( \frac{1}{n} \sum_{i=1}^n X_i \right) \left( \frac{1}{n} \sum_{i=1}^n X_i \right) \right] \\ \\ &= \frac{1}{n^2} \ \mathbb{E}\left[ \left(\sum_{i=1}^n X_i \right) \left( \sum_{i=1}^n X_i \right) \right] \\ \\ &= \frac{1}{n^2}\ \mathbb{E}\left[ \left(\sum_{i=1}^n X_i^2 \right) + \left( \sum_{i\neq j} X_i X_j\right) \right] \\ \\ \mathbb{E}[\hat{\mu}^2]  &= \frac{1}{n^2}\ \left[\sum_{i=1}^n \mathbb{E}[X_i^2] + \sum_{i\neq j} \mathbb{E}[X_i X_j] \right] \end{align}$$

Since, all $X_i$ are *identically distributed*, $\mathbb{E}[X_i^2] = \mathbb{E}[X^2]$
And since, all $X_i$ *independent*
- using $\sigma^2 = \mathbb{E}[X^2] - \mu^2$, I can state  $\mathbb{E}[X^2] = \sigma^2 + \mu^2$
- $\mathbb{E}[X_i X_j] = \mathbb{E}[X_i]\mathbb{E}[X_j] = \mathbb{E}[X]\mathbb{E}[X] = \mu^2$

For $\sum_{i \neq j}\mathbb{E}[X_iX_j]$, there are a total of $n(n-1)$ terms. Thus, $$\sum_{i \neq j}\mathbb{E}[X_iX_j] = \sum_{i \neq j}\mathbb{E}[X]\mathbb{E}[X] = n(n-1)\mu^2$$
Substituting this in back gives me,
$$\begin{align} \mathbb{E}[\hat{\mu}^2]  &= \frac{1}{n^2}\ \left[\sum_{i=1}^n \mathbb{E}[X_i^2] + \sum_{i\neq j} \mathbb{E}[X_i X_j] \right] \\ \\ &=  \frac{1}{n^2}\ \left[n \mathbb{E}[X_i^2] + n(n-1)\mu^2 \right] \\ \\ &= \frac{1}{n^2}\ \left[n(\sigma^2 + \mu^2) + n^2\mu^2 - n\mu^2 \right] \\ \\ \mathbb{E}[\hat{\mu}^2] &= \frac{\sigma^2}{n} + \mu^2 \end{align}$$

> [!tip]
> Another way to derive $\mathbb{E}[\hat{\mu}^2]$ would be through the definition of variance.
> $$ \text{Var}(\hat{\mu}) = \text{Var}\left( \frac{1}{n} \sum_{i=1}^n X_i \right) $$
>
> And cause $\text{Var}(aY) = a^2\text{Var}(Y)$:
> $$\text{Var}(\hat{\mu}) = \frac{1}{n^2} \text{Var}\left( \sum_{i=1}^n X_i \right) $$
>
> Because $X_i$ are independent, the variance of the sum equals the sum of the variances (covariance terms are zero).
> $$ = \frac{1}{n^2} \sum_{i=1}^n \text{Var}(X_i) $$
>
> Since $\text{Var}(X_i) = \sigma^2$:
> $$ = \frac{1}{n^2} (n\sigma^2) = \frac{\sigma^2}{n} $$
>
> Also, recall that $\text{Var}(Y) = \mathbb{E}[Y^2] - (\mathbb{E}[Y])^2$. Applying this to $\hat{\mu}$:
> $$ \mathbb{E}[\hat{\mu}^2] = \text{Var}(\hat{\mu}) + (\mathbb{E}[\hat{\mu}])^2 = \frac{\sigma^2}{n} + \mu^2 $$


Now, I can finally find the variance of the estimator $\hat{\mu}$,
$$\begin{align} \text{Var}(\hat{\mu}) &= \mathbb{E}\Big[ (\hat{\mu} - \mathbb{E}[\hat{\mu}])^2 \Big] = \mathbb{E}[\hat{\mu}^2] - (\mathbb{E}[\hat{\mu}])^2 \\ \\  \text{Var}(\hat{\mu}) &= \left(\frac{\sigma^2}{n} + \mu^2\right) - \mu^2 = \frac{\sigma^2}{n} \end{align}$$

And as $n\rightarrow \infty$, the $\text{Var}(\hat{\mu})$ approaches zero.
$$\lim_{n \rightarrow \infty} \text{Var}(\hat{\mu}) = \lim_{n \rightarrow \infty}\frac{\sigma^2}{n} = 0$$

Variance of sample mean estimator becomes smaller for larger sample size. The spread of the sampling distribution collapses to zero. The probability mass concentrates entirely on a single point.

This proof confirms that $\hat{\mu}$ is a **consistent estimator** of $\mu$.

#### Variance as an Estimator

When I estimate the population variance (my parameter) using the sample variance (my estimator), I divide by $n-1$ instead of [[Measures of Variability#Why divide by $n-1$ ?|dividing by the sample size]]. I can prove that doing this actually makes variance a better estimator.

1. Prove that the **Naive Sample Variance** estimator $\hat{\sigma}^2_{bias} = \frac{1}{n} \sum (X_i - \hat{\mu})^2$ is **Biased**.
2. Prove that the **Corrected Sample Variance** estimator $\hat{\sigma}^2 = \frac{1}{n-1} \sum (X_i - \hat{\mu})^2$ is **Unbiased**

###### Proof 1: Bias of the Naive Variance Estimator

Let's say I use $\hat{\sigma}^2_{bias}$ as an estimator for population variance $\sigma^2$. Where,
$$\hat{\sigma}^2_{bias} = \frac{1}{n} \sum (X_i - \hat{\mu})^2$$

The bias of an estimator $\hat{\theta}$ for a parameter $\theta$ is given by, $\text{Bias}(\hat{\theta})= \mathbb{E}[\hat{\theta}] - {\theta}$. Here, $\hat{\sigma}^2_{bias}$ is the estimator for the parameter $\sigma^2$. So, bias of may estimator $\hat{\sigma}^2_{bias}$ is,
$$\text{Bias}(\hat{\sigma}^2_{bias}) = \mathbb{E}[\hat{\sigma}^2_{bias}] - \sigma^2$$

Finding $\mathbb{E}[\hat{\sigma}^2_{bias}]$

$$\begin{align} \mathbb{E}[\hat{\sigma}^2_{bias}] &= \mathbb{E}\left[ \frac{1}{n} \sum_{i=1}^n (X_i - \hat{\mu})^2 \right] \\ \\ &= \frac{1}{n} \ \mathbb{E}\left[\sum_{i=1}^n (X_i^2 - 2X_i\hat{\mu} + \hat{\mu}^2)\right] \\ \\ &= \frac{1}{n} \mathbb{E} \left[\sum_{i=1}^{n} X_i^2 - 2\hat{\mu}\sum_{i=1}^{n} X_i + \sum_{i=1}^{n} \hat{\mu}^2 \right]\end{align}$$

Substitute $\sum X_i = n\hat{\mu}$ (since $\hat{\mu} = \frac{\sum X_i}{n}$):
$$\begin{align} \mathbb{E}[\hat{\sigma}^2_{bias}] &= \frac{1}{n} \mathbb{E} \left[\sum_{i=1}^{n} X_i^2 - 2n\hat{\mu}^2 + n\hat{\mu}^2  \right] \\ \\ &= \frac{1}{n} \mathbb{E} \left[\sum_{i=1}^n X_i^2 - n\hat{\mu}^2  \right]\\ \\ &= \frac{1}{n} \left[\sum_{i=1}^n \mathbb{E}[X_i^2] - n\mathbb{E}[\hat{\mu}^2]  \right]\end{align}$$

Substituting,
- $\mathbb{E}[X_i^2] = \mathbb{E}[X^2] = \sigma^2 + \mu^2$
- $\mathbb{E}[\hat{\mu}^2] = \frac{\sigma^2}{n} + \mu^2$

$$\begin{align} \mathbb{E}[\hat{\sigma}^2_{bias}] &=  \frac{1}{n} \ \left[ n(\sigma^2 + \mu^2) -  n(\frac{\sigma^2}{n} + {\mu}^2) \right] \\ \\ &= \sigma^2 - \frac{\sigma^2}{n} \\ \\ \mathbb{E}[\hat{\sigma}^2_{bias}] &= \frac{n-1}{n}\sigma^2 \end{align}$$

Finally, the bias would be:
$$\begin{align} \text{Bias}(\hat{\sigma}^2_{bias}) &= \mathbb{E}[\hat{\sigma}^2_{bias}] - \sigma^2 \\ \\  &= \frac{n-1}{n}\sigma^2 - \sigma^2 \\ \\  &= \frac{-\sigma^2}{n}\end{align}$$
My bias is negative, that means the estimator $\hat{\sigma}^2_{bias}$ would *always underestimate the value of the population variance.* This also tracks with [[Measures of Variability#Why divide by $n-1$ ?#Underestimation of Population Variance|what I already know]].

Also as $n\rightarrow \infty$, the $\text{Bias}(\hat{\sigma}_{bias}^2)$ approaches zero.
$$\lim_{n \rightarrow \infty} \text{Bias}(\hat{\sigma}_{bias}^2) = \lim_{n \rightarrow \infty}\frac{-\sigma^2}{n} = 0^{-}$$

Although **$\hat{\sigma}^2_{bias} = \frac{1}{n} \sum (X_i - \hat{\mu})^2$ is a biased estimator for the population variance**, as the sample size gets larger and larger, the bias does tend to approach zero.
I can say, **$\hat{\sigma}^2_{bias} = \frac{1}{n} \sum (X_i - \hat{\mu})^2$ is an Asymptotically un-biased estimator for the population variance.**

---

###### Proof 4: Unbiasedness of Sample Variance

I want to prove that dividing by $n-1$ instead of $n$ fixes the bias.

Let $\hat{\sigma}^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i - \hat{\mu})^2$.

$$ \mathbb{E}[\hat{\sigma}^2] = \frac{1}{n-1} \mathbb{E}\left[ \sum_{i=1}^{n} (X_i - \hat{\mu})^2 \right] $$

From Proof 3, I know the term in the brackets is $(n-1)\sigma^2$:
$$ = \frac{1}{n-1} \left( (n-1)\sigma^2 \right) $$
$$ = \sigma^2 $$

The estimator $\hat{\sigma}^2$ is **Unbiased**.
