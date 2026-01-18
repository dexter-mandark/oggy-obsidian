---
cssclasses:
  - callouts-outlined
---

###### Tags:

---


A fundamental task in many statistical analyses is to estimate **a location parameter for the distribution**; i.e., to find a typical or central value that best describes the data.

**Measures of central tendency** are statistical values that represent the *center or typical value* of a dataset, providing a single number that summarizes the entire distribution. This center value is the value that is most representative of the dataset as the whole.

The **three main measures** of central tendency are mean, median, and mode.

## Mean

The mean of a dataset is the unique value for which the sum of the deviations of all data points from that value is exactly zero.
*Mean, by definition is the "centroid" of the data.*

#### Mean for uni-variate data

For a dataset with $n$ scalar values $\{x_{1},x_{2},…,x_{n}\}$, where a non-negative weight $w_{i}$ is associated with each data-point $x_{i}$, representing its relative importance.

The mean ($\mu$ or $\bar{x}$) is just the average weighted value. $$ \mu = \frac{\sum_{i=1}^{n}w_{i} x_{i}}{\sum_{i=1}^{n} w_{i}}$$
If each data point has an equal weighted, the mean is called **arithmetic mean**. $$\mu = \frac{1}{n}\sum_{i=1}^{n} x_{i}$$

>[!tip] Weighted Mean
>
>For weighted mean, the weighted sum of deviations from the mean is zero.$$ \sum_{i=1}^{n}w_{i}(x_{i}−\mu)=0$$

#### Mean for multi-variate data

For a dataset with $n$ data points, where each data point is a $d$-dimensional vector $\mathbf{x}_{i} = [x_{1}^{i}, x_{2}^{i}, \cdots, x_{d}^{i}]$, the mean is a vector $\boldsymbol{\mu} = [\mu_{1}, \mu_{2}, \cdots, \mu_{d}]$, defined as, $$\boldsymbol{\mu} = \frac{1}{n}\sum_{i=1}^{n} \mathbf{x}_{i}$$

Where each component $μ_{j}$ of the mean vector is the arithmetic mean of the $j$-th component of all data vectors. $\mu_j = \frac{1}{n}\sum_{i=1}^{n} x^i_j$

 If each vector $\mathbf{x}_{i}$ has a corresponding scalar weight $w_i$ then, $$\boldsymbol{\mu} = \frac{\sum_{i=1}^{n} w_{i} \ \mathbf{x}_{i}}{\sum_{i=1}^{n} w_{i}}$$

### Generalizing the Definition

The mean is fundamentally defined as the **expected value**.

>[!quote] Probabilistic definition of mean
>The mean or expected value $E[X]$ of [[Random Variable#^def-random-variable|random variable]] $X$ is defined as:
>
>- For discrete distributions: $$E[X]= \sum_{i=1}^{n} x_{i}⋅P(X=x_{i})$$
>
> - For continuous distributions: $$E[X]= \int_{-\infty}^{\infty}{x⋅f(x)dx}$$
^def-mean-generic

### Problems with Mean

Mean is the most commonly used measure of central tendency, but it has significant limitations that can make it a misleading representation of a dataset if considered in isolation.

##### High Sensitivity to Outliers

The sum of deviation of all data points from the mean is zero. $\sum_{i=1}^{n}(x_{i}−\mu)=0$. This implies that mean is *sensitive to the value of every data point*.

Because the mean is calculated using the value of every single data point, it is sensitive to extreme values, or outliers. An outlier, which is a data point that differs significantly from other observations, can pull the mean towards it.

>[!example]
>Consider the salaries of employees at a small company. If nine employees earn between $50,000 and $60,000, and the CEO earns $1,500,000, the mean salary will be significantly inflated by the CEO's salary. It will not accurately reflect the "typical" salary of an employee. In such cases, *the median often provides a more robust measure of central tendency.*

##### Inadequate for Skewed or Multi-modal Distributions

The mean is most representative when the data is symmetrically distributed (like in a normal distribution).
- For skewed distributions, the mean is pulled towards the long tail, away from the central cluster of data.
- For multi-modal distributions (datasets with more than one peak or "mode"), the mean can fall in a "valley" between peaks, representing a value that is not typical at all.

>[!example]
>- **Skewed Data**: In a dataset of housing prices, which is often right-skewed by a few very expensive properties, the mean price will be higher than what a typical buyer might expect.
>- **Multi-modal Data**: A dataset of restaurant ratings might have peaks at 1-star (very unhappy customers) and 5-stars (very happy customers). The mean rating could be 3 stars, suggesting "average" satisfaction, which fails to capture the polarized reality of customer opinions.

##### Provides No Information About Data Spread or Shape

The mean only locates the center of a dataset; it says nothing about the **dispersion** (spread), **variability**, or the **shape** of the distribution. Two datasets can have identical means but be completely different in their structure.

![[different-distributions-equal-means.png|#invert_B]]