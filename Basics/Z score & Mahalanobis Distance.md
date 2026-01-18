---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Measures of Variability]]

---

## Distance ain't gonna cut it

I'm always interested in identifying how far a data point is from the mean, or where a data point is in relation to other data points.

[[Norm#^def-minkowski-distance|Distance (Minkowski Distance)]] seems like an intuitive measure that I would consider when comparing data points. However, raw distance measurements are fundamentally flawed for comparison because they ignore the **scale** and **variability** inherent in data, as well as the **correlations** between different dimensions.​

#### Problems with Distance

- **Scale-Dependence:** Distance is highly sensitive to the scale of the variables. If one variable has a much larger range of values than another (e.g., salary in dollars vs. years of experience), it will dominate the distance calculation. This can lead to misleading conclusions about the similarity between data points.

- **Ignoring Correlation:** Distance assumes that all variables are independent and uncorrelated. However, in real-world data, variables often have strong correlations (e.g., height and weight). Distance fails to account for this, treating correlated variables as if they provide entirely separate pieces of information. This can distort the true relationship between data points.

- **Ignoring Variance:** The measure does not consider the variance within each variable. A difference of 10 units in a variable with low variance is much more significant than the same difference in a variable with high variance. Euclidean distance treats both equally.

##### Comparisons Between Two Data Points in Different Distributions

Consider two uni-variate distributions: $D_{1} (\mu_{1},\sigma_{1})$ and $D_2(\mu_2,\sigma_{2})$. Lets say $x$ is $5$ units away from the $\mu_{1}$ in $D_{1}$, and $y$ is $20$ units away from $\mu_{2}$ in distribution $D_{2}$. Simply giving the raw distances $(x - \mu_{1})$ and $(y - \mu_{2})$ doesn't tell me much.
- Without accounting for the variability in each distribution, I cannot meaningfully compare how "unusual" or "extreme" these data points are.
- The raw distance of $20$ does not tell me that $y$ is more extreme than $x$; in fact, relative to their respective distributions, $x$ might be the more unusual value.​

##### Comparisons Between Two Data Points in the Same Distribution

Even when comparing data points within the same multivariate distribution, raw distance still fails me. Because different features (dimensions) can have vastly different scales and variances.

In a high-variance dimension, even a large raw deviation might not be statistically significant, while in a low-variance dimension, even a small raw deviation could be highly significant.
Raw distance treats all dimensions equally, which leads to misleading conclusions about how unusual or extreme a data point is.​

If I were to compute the Euclidean distance between a data point and the mean, the calculation would be dominated by whichever dimension has the larger numerical scale, regardless of its actual variability. This means that dimensions with larger units (like height in cm versus weight in kg) would disproportionately influence the distance measure.​

##### What to do ?

I need a measure that takes into account the **context** of each distribution, specifically the spread and variability of the distribution.
*Can't compare apple with oranges, so first I gotta level the playing field*.

## Z score

>[!cite] Z-Score
>A **z-score**, also known as a **standard score**, is expressed in terms of standard deviations from the mean, indicating how many standard deviations a data point $x$ is above or below the mean.
>
> $$z=\frac{x-\mu}{\sigma}$$
^def-zscore

Z-score is a **standardization** because it converts data to a common scale with **mean 0 and standard deviation 1.​**

The sign and magnitude of the z-score provide a clear interpretation of a data point's position within its distribution:​
- **Positive z-score:** The raw score is above the mean. A z-score of $+2$ means the data point is $2$ standard deviations above the mean.
- **Negative z-score:** The raw score is below the mean. A z-score of $-1.5$ means the data point is $1.5$ standard deviations below the mean.
- **Zero z-score:** The raw score is exactly equal to the mean.

![[grading methods in a normal distribution.png|#invert_B]]

#### What Works

###### Comparing Different Datasets

Z-scores *levels the playing field* by expressing deviations in terms of standard deviations, which are standardized units of measurement. It takes care of the "scale" problem. Lets say the data point $x$ in distribution $D_{1}$ has the z-score $z_{x} = 0.6$ and data point $y$ in $D_{2}$ has the z-score $z_{y} = 3.0$, you can absolutely make a lot of conclusions about $x$ and $y$, even if I don't tell you anything about the distributions themselves.

> [!example]
> A student scores $70$ on an English test ($\mu=60, \sigma=15$) and $80$ on a Math test ($\mu=85, \sigma=5$). Which score is better relative to the class?
> -  English z-score: $(70 - 60) / 15 = +0.67$
> - Math z-score: $(80 - 85) / 5 = -1.0$
>     
> The positive z-score in English shows the student performed above average, while the negative z-score in Math shows they performed below average, despite the higher raw score.
>

###### Identifying Outliers

Z-scores help identify data points that are significantly different from the rest of the dataset. Data points with z-scores greater than $+3$ or less than $-3$ are often considered outliers because they are very far from the mean.​

###### Calculating Probabilities

In a normal distribution, z-scores can be used with a z-table to find the probability of a score occurring. For example, a z-score of 0 corresponds to the 50th percentile.

#### The Limitation of Z-Scores: Ignoring Correlations

Z-scores work well for uni-variate data or when features are **uncorrelated**. However, in multivariate data, when correlations exist, z-scores alone are insufficient because they treat each dimension independently.​

Suppose I'm analyzing student performance with two features: hours studied and exam score. These variables are highly correlated.​
Consider two students:
- Oggy: studied 5 hours, scored 70% (slightly above average in both)
- Jack: studied 2 hours, scored 85% (below average in study time, well above average in score)

If I compute z-scores for each dimension independently and then calculate a distance, Jack might appear similar to the mean in aggregate. However, Jack's combination of low study time and high score is actually **highly unusual** given the strong positive correlation between these variables. *Standard z-score distance metrics would fail to capture this anomaly because they don't account for the relationship between dimensions.*

>[!danger]
>- When variables are correlated, the "shape" of the data distribution is not a simple sphere or ellipse aligned with the axes. Instead, it's an ellipse tilted at an angle, reflecting the correlation structure.
>- Euclidean distance (even with z-scores) assumes the axes are independent, which distorts the understanding of how far a point truly is from the center of the distribution.

This is where Mahalanobis distance comes in.

## Mahalanobis Distance

The Mahalanobis distance (named after *Prasanta Chandra Mahalanobis*) is a **unit-less, scale-invariant, multivariate generalization of the z-score** that accounts for both the variance of each variable and the covariance between variables.


If the variables are uncorrelated and have a variance of 1, the Mahalanobis distance becomes equivalent to the standard Euclidean distance.
