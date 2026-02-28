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
> *Rather than asking how far a data-point is from the mean, I ask "How many standard deviations away is this data-point from the mean ?"*
>
> Z-score is a **standardization** because it converts data to a common scale with **mean 0 and standard deviation 1.​**
^def-zscore


The sign and magnitude of the z-score provide a clear interpretation of a data point's position within its distribution:​
- **Positive z-score:** The raw score is above the mean. A z-score of $+2$ means the data point is $2$ standard deviations above the mean.
- **Negative z-score:** The raw score is below the mean. A z-score of $-1.5$ means the data point is $1.5$ standard deviations below the mean.
- **Zero z-score:** The raw score is exactly equal to the mean.

![[grading methods in a normal distribution.png|#invert_B]]

#### Where Z-score Shines

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

#### Where Z-score Fails: Ignores Correlations

Z-scores work well for uni-variate data or when features are **uncorrelated**. However, in multivariate data, when correlations exist, z-scores alone are insufficient because they treat each dimension independently.​

Suppose I'm analyzing student performance with two features: hours studied and exam score. These variables are highly correlated.​
- Mean hours studied: $\mu_{\text{hrs}} = 5$, with $\sigma_{\text{hrs}} = 2$
- Mean exam score: $\mu_{\text{score}} = 70\%$, with $\sigma_{\text{score}} = 10$
- Strong positive correlation: $r = 0.9$ (study more $\rightarrow$ score higher)

Consider three students:

| Student | Hours Studied | Exam Score |
| ------- | ------------- | ---------- |
| Jack    | 6 hrs         | 75%        |
| Oggy    | 2 hrs         | 85%        |
| Bob     | 8 hrs         | 85%        |
If I compute z-scores for each dimension independently and then calculate a distance:

| Student | $z_{\text{hrs}}$ | $z_{\text{score}}$  | Euclidean Z-distance $\sqrt{z_{\text{hrs}}^2 + z_{\text{score}}^2}$ |
| ------- | ---------------- | ------------------- | ------------------------------------------------------------------- |
| Jack    | $(6-5)/2 = +0.5$ | $(75-70)/10 = +0.5$ | $\sqrt{0.25 + 0.25} \approx 0.71$                                   |
| Oggy    | $(2-5)/2 = -1.5$ | $(85-70)/10 = +1.5$ | $\sqrt{2.25 + 2.25} \approx 2.12$                                   |
| Bob     | $(8-5)/2 = +1.5$ | $(85-70)/10 = +1.5$ | $\sqrt{2.25 + 2.25} \approx 2.12$                                   |

Oggy and Bob have identical Euclidean z-distances from the mean. According to z-scores, they are equally anomalous. But intuitively this is wrong, Bob studied a lot and scored high, which is exactly what I'd expect given the strong correlation. However Oggy's
combination of low study time and high score is actually **highly unusual** given the strong positive correlation between these variables.

*The problem is the direction of the deviation, not just the magnitude.* Given $r = 0.9$, the "expected" pattern is: high hour $\leftrightarrow$ high score, low hours $\leftrightarrow$ low score
- Bob has $z_{\text{hrs}} = +1.5$ and $z_{\text{score}} = +1.5$: both deviations are in the **same direction**, consistent with the correlation. They sit _along_ the data ellipse.
- Oggy has $z_{\text{hrs}} = -1.5$ and $z_{\text{score}} = +1.5$: both deviations in **opposite directions**, cutting _across_ the correlation structure. He sits far off the diagonal band.

**Z-score distance is blind to the co-relation between the two features because it only uses $|z_j|$, not whether the z-scores are jointly consistent with the covariance structure.**

>[!danger]
>- When variables are correlated, the "shape" of the data distribution is not a simple sphere or ellipse aligned with the axes. Instead, it's an ellipse tilted at an angle, reflecting the correlation structure.
>- Euclidean distance (even with z-scores) assumes the axes are independent, which distorts the understanding of how far a point truly is from the center of the distribution.

This is where Mahalanobis distance comes in.

## Mahalanobis Distance

The Mahalanobis distance (named after *Prasanta Chandra Mahalanobis*) is a **unit-less, scale-invariant, multivariate generalization of the z-score** that accounts for both the variance of each variable and the covariance between variables.

- Z-score distance asks: "How far is this point from the mean, measured in standard deviations, along each axis independently?"
- Mahalanobis distance asks: **"How far is this point from the mean, measured relative to the natural shape and orientation of the entire data distribution?"**​

> [!tip] Mahalanobis Distance vs Euclidean Distance
> In a regular Euclidean space, variables (e.g. $x, y, z$) are represented by axes drawn at right angles to each other. The distance between any two points can be measured with a ruler. *For uncorrelated variables, the Euclidean distance equals the Mahalanobis distance.*
> However, if two or more variables are correlated the axes are no longer at right angles, and the measurements become impossible with a ruler.
>
> In addition, if you have more than three variables, you can’t plot them in regular 3-D space at all. Mahalanobis distance solves this measurement problem, as it measures distances between points, even correlated points for multiple variables.
>
> The key shift is that Mahalanobis distance treats the data cloud itself as the ruler.
> - If the data is stretched along a diagonal (due to correlation), then moving along that diagonal is "cheap", it's expected.
> - Moving perpendicular to it is "expensive", it's genuinely anomalous.
> 
> Z-score distance has no concept of this; it treats every axis as equally independent and equally scaled.

#### Deriving Mahalanobis Distance

Let's start with the simplest case: **one dimension**. The natural "distance" from the mean, normalized by the spread, is just the Z-score:
$$d = \frac{x - \mu}{\sigma} = (x - \mu) \cdot \sigma^{-1}$$
Now, for **two uncorrelated dimensions**. There's no tilt to the ellipse (formed by the data cloud), it's axis-aligned. I can independently normalize each axis by its own standard deviation and compute Euclidean distance in that normalized space.
For a data-point $x =\begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$ and mean $\mu = \begin{bmatrix}  \mu_1 \\ \mu_2 \end{bmatrix}$. The Mahalanobis distance $D^2$ is given by:
$$D^2 = \frac{(x_1 - \mu_1)^2}{\sigma_1^2} + \frac{(x_2 - \mu_2)^2}{\sigma_2^2}$$

With no correlation, the covariance matrix is diagonal.$$\begin{align} C &= \begin{bmatrix} \sigma_1^2 & 0 \\ 0 & \sigma_2^2 \end{bmatrix} \\ \\ C^{-1} &= \begin{bmatrix} 1/\sigma_1^2 & 0 \\ 0 & 1/\sigma_2^2 \end{bmatrix} \end{align}$$
The vector representing the distance between $x$ and mean $\mu$ is the called the **deviation vector**. $$\mathbf{d} = x - \mu = \begin{bmatrix}  (x_1 - \mu_1) \\ (x_2 - \mu_2) \end{bmatrix}$$

Lets say I multiply $\mathbf{d}$ with $C^{-1}$, just for the sake of it. Then again multiply the whole thing with $\mathbf{d}^T$.

$$\begin{align} \mathbf{d}^T C^{-1} \mathbf{d} &= \begin{bmatrix}  (x_1 - \mu_1) &  (x_2 - \mu_2) \end{bmatrix} \begin{bmatrix} \frac{1}{\sigma_1^2} & 0 \\ 0 & \frac{1}{\sigma_2^2} \end{bmatrix} \begin{bmatrix}  (x_1 - \mu_1) \\ (x_2 - \mu_2) \end{bmatrix} \\ &= \begin{bmatrix}  (x_1 - \mu_1) &  (x_2 - \mu_2) \end{bmatrix} \begin{bmatrix} \frac{(x_1 - \mu_1)}{\sigma_1^2} & 0 \\ 0 & \frac{(x_1 - \mu_1)}{\sigma_2^2} \end{bmatrix} \\ \\ &= \frac{(x_1 - \mu_1)^2}{\sigma_1^2} + \frac{(x_2 - \mu_2)^2}{\sigma_2^2} \\ &= D^2 \end{align}$$


So the expression above is exactly , which is the Mahalanobis distance squared. In the uncorrelated case it reduces perfectly to the scaled Z-score distance.​

Now add **correlation**. The ellipse tilts. The covariance matrix $C$ now has non-zero off-diagonal entries that encode this tilt. Its inverse $C^{-1}$ — called the **precision matrix** — encodes the same correlation structure, but inverted: it tells you which directions are "tight" (penalised heavily) versus "loose" (penalised lightly). Plugging $C^{-1}$ into the same formula automatically rotates the distance measurement to align with the ellipse.[[builtin](https://builtin.com/data-science/mahalanobis-distance)]​



Given a dataset with $N$ $d$-dimensional data points $\mathbf{x}^i \in \mathbb{R}^d$, mean vector $\boldsymbol{\mu} \in \mathbb{R}^d$, and covariance matrix $C \in \mathbb{R}^{d \times d}$, the Mahalanobis distance of a point $\mathbf{x}$ from the mean is:​

$$D_M(\mathbf{x}) = \sqrt{(\mathbf{x} - \boldsymbol{\mu})^T \, C^{-1} \, (\mathbf{x} - \boldsymbol{\mu})}$$

Let $\mathbf{d} = \mathbf{x} - \boldsymbol{\mu}$ be the deviation vector. Then:
$$D_M^2(\mathbf{x}) = \mathbf{d}^T C^{-1} \mathbf{d}$$

This is a **scalar**, a weighted quadratic form. The matrix $C^{-1}$ sits in the middle and acts as the weighting operator, stretching and rotating the deviation vector before taking the dot product.

## What $C^{-1}$ Is Actually Doing

It helps to think of $C^{-1}$ as performing two operations simultaneously:[[mccormickml](https://mccormickml.com/2014/07/22/mahalanobis-distance/)]​

**Rescaling:** Dimensions with large variance contribute less to the distance (they're penalised by $1/\sigma^2$). Dimensions with small variance contribute more. This is the same thing Z-scores do.

**Rotating:** The off-diagonal terms of $C^{-1}$ add cross-terms between dimensions. Concretely, for the 2D case with covariance $\sigma_{XY}$:

C−1=1det⁡(C)[σY2−σXY−σXYσX2]C^{-1} = \frac{1}{\det(C)}\begin{bmatrix} \sigma_Y^2 & -\sigma_{XY} \\ -\sigma_{XY} & \sigma_X^2 \end{bmatrix}C−1=det(C)1[σY2−σXY−σXYσX2]

The negative $-\sigma_{XY}$ terms penalise deviations that go against the correlation (like Jack's low hours + high score) and effectively credit deviations that go with it (like Student C's high hours + high score). This is exactly what Z-scores fail to do.[[mccormickml](https://mccormickml.com/2014/07/22/mahalanobis-distance/)]​

## Key Properties

**Scale invariance:** Multiplying any variable by a constant doesn't change the Mahalanobis distance, because the scaling gets absorbed into $C$ and cancelled by $C^{-1}$.[[shadecoder](https://www.shadecoder.com/topics/mahalanobis-distance-a-comprehensive-guide-for-2025)]​

**Rotation invariance:** Rotating the coordinate axes doesn't change Mahalanobis distance either — it always measures distance relative to the data's own geometry, not the coordinate system.[[en.wikipedia](https://en.wikipedia.org/wiki/Mahalanobis_distance)]​

**Reduces to Euclidean distance** when $C = I$ (the identity matrix), i.e., all variables have unit variance and zero covariance.[[mccormickml](https://mccormickml.com/2014/07/22/mahalanobis-distance/)]​

**Reduces to Z-score distance** when $C$ is diagonal (variables are uncorrelated but may have different variances).[[mccormickml](https://mccormickml.com/2014/07/22/mahalanobis-distance/)]​

So Mahalanobis distance is the most general form — Euclidean distance and Z-score distance are both special cases of it.





If the variables are uncorrelated and have a variance of 1, the Mahalanobis distance becomes equivalent to the standard Euclidean distance.


Let's set up concrete numbers.

## The Dataset Setup

Suppose from a class of students we observe:

- Mean hours studied: $\mu_{\text{hrs}} = 5$, with $\sigma_{\text{hrs}} = 2$
    
- Mean exam score: $\mu_{\text{score}} = 70%$, with $\sigma_{\text{score}} = 10$
    
- Strong positive correlation: $r = 0.9$ (study more → score higher)
    

Our three students:

## Step 1 — Compute Z-scores

For each student, standardize each dimension independently:

z=x−μσz = \frac{x - \mu}{\sigma}z=σx−μ

## Step 2 — What Z-scores Miss

The problem is the direction of the deviation, not just the magnitude. Given $r = 0.9$, the "expected" pattern is:

high hours↔high score,low hours↔low score\text{high hours} \leftrightarrow \text{high score}, \quad \text{low hours} \leftrightarrow \text{low score}high hours↔high score,low hours↔low score

- **Student C** has $z_{\text{hrs}} = +1.5$ and $z_{\text{score}} = +1.5$ — both deviations in the **same direction**, consistent with the correlation. They sit _along_ the data ellipse.
    
- **Jack** has $z_{\text{hrs}} = -1.5$ and $z_{\text{score}} = +1.5$ — deviations in **opposite directions**, cutting _across_ the correlation structure. He sits far off the diagonal band.
    

Z-score distance is blind to this because it only uses $|z_j|$, not whether the z-scores are jointly consistent with the covariance structure.

## Step 3 — Mahalanobis Sees the Difference

The covariance matrix with these parameters is:

C=[σhrs2r σhrs σscorer σhrs σscoreσscore2]=[41818100]C = \begin{bmatrix} \sigma_{\text{hrs}}^2 & r\,\sigma_{\text{hrs}}\,\sigma_{\text{score}} \\ r\,\sigma_{\text{hrs}}\,\sigma_{\text{score}} & \sigma_{\text{score}}^2 \end{bmatrix} = \begin{bmatrix} 4 & 18 \\ 18 & 100 \end{bmatrix}C=[σhrs2rσhrsσscorerσhrsσscoreσscore2]=[41818100]

With $\det(C) = 4 \times 100 - 18^2 = 76$, the inverse is:

C−1=176[100−18−184]C^{-1} = \frac{1}{76}\begin{bmatrix} 100 & -18 \\ -18 & 4 \end{bmatrix}C−1=761[100−18−184]

The Mahalanobis distance squared is $D^2 = \mathbf{d}^T C^{-1} \mathbf{d}$, where $\mathbf{d} = \mathbf{x} - \boldsymbol{\mu}$ is the raw deviation vector.

**For Jack:** $\mathbf{d} = \begin{bmatrix} 2-5 \ 85-70 \end{bmatrix} = \begin{bmatrix} -3 \ 15 \end{bmatrix}$

D2=176[−315][100−18−184][−315]=176(1710+1710)=45  ⟹  D≈6.71D^2 = \frac{1}{76}\begin{bmatrix}-3 & 15\end{bmatrix}\begin{bmatrix} 100 & -18 \\ -18 & 4 \end{bmatrix}\begin{bmatrix}-3\\15\end{bmatrix} = \frac{1}{76}(1710 + 1710) = 45 \implies D \approx 6.71D2=761[−315][100−18−184][−315]=761(1710+1710)=45⟹D≈6.71

**For Student C:** $\mathbf{d} = \begin{bmatrix} 3 \ 15 \end{bmatrix}$

D2=176[315][100−18−184][315]=176(90+90)≈2.37  ⟹  D≈1.54D^2 = \frac{1}{76}\begin{bmatrix}3 & 15\end{bmatrix}\begin{bmatrix} 100 & -18 \\ -18 & 4 \end{bmatrix}\begin{bmatrix}3\\15\end{bmatrix} = \frac{1}{76}(90 + 90) \approx 2.37 \implies D \approx 1.54D2=761[315][100−18−184][315]=761(90+90)≈2.37⟹D≈1.54

## The Punchline

|Student|Euclidean Z-distance|Mahalanobis Distance|
|---|---|---|
|Oggy|0.71|0.51 — unremarkable|
|Jack|2.12|**6.71** — genuinely anomalous|
|Student C|2.12|1.54 — expected pattern|

Z-scores declare Jack and Student C equally unusual. Mahalanobis correctly identifies Jack as far more anomalous (distance 6.71 vs 1.54), because it accounts for the fact that Jack's low-hours + high-score combination cuts directly against the correlation structure of the data. The $C^{-1}$ term in Mahalanobis is doing the heavy lifting — it _penalises_ deviations that violate the expected covariance pattern and _rewards_ deviations that follow it.