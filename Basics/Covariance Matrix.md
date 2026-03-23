---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Measures of Variability]]

---

## Variance for multi-variate data

For simple, uni-variate data (data with only one variable), [[Measures of Variability#^def-variance|standard deviation]] works perfectly. It gives me a single number representative of the average spread of the data.

A larger standard deviation means the data points are much more spread out from the mean, and a smaller standard deviation means the data points are clustered together around the mean.

The problem arises when I have **multivariate** data. I can still define variance for multi-variate data, as the mean squared [[Norm#^def-euclidean-distance|Euclidean distance]] from the mean of the data cloud.

>[!cite] Multi-variate variance
>- Each data point $\mathbf{x}^{i}$ is a vector, The mean $\boldsymbol{\mu}$ is also a vector.
>- The term $\mathbf{x}^{i} - \boldsymbol{\mu}$ is the **deviation vector**, showing how far the point is from the center in each dimension.
>- And $\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}$ is the square of the L2 norm of the deviation vector.
>- Average all these and I have my variance. $\rightarrow$
>
> $$\sigma = \frac{1}{N}{\sum_{i=1}^{N}\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}}$$
>
> This single value is a perfectly valid *measure of the overall spread*. This gives me a single number that represents, on average, how far the data points are from the central mean, treating all directions of spread equally.
^def-multi-variate-cariance

But that's where the problem lies: *A single number cannot describe the shape and orientation of the data.*

The problem is that treating all directions of spread equally is precisely where the information is lost. This single value assumes the data cloud is roughly **spherical** or circular, where the spread is the same in every direction.
However, in most real-world scenarios, the data is not spherical. This is because:
1. **Variables have different variances:** The spread along one axis (e.g., salary in dollars) might be thousands of times larger than the spread along another (e.g., years of experience).
2. **Variables are correlated:** If two variables are correlated, the data cloud becomes stretched and tilted into an elliptical shape.

The Euclidean-based variance measure averages these different directional spreads into a single number, effectively hiding the true shape of the data. For instance, an elliptical data cloud that is very wide but not very tall could have the same average "distance-from-center" as a perfectly circular cloud, even though their structures are completely different.

That's where the covariance matrix comes in !

## Covariance Matrix

**Variance by itself does not capture the directionality of dispersion**.
- I must consider the variance of each component i.e. $\sigma_{X}$
- And consider how that component relates to other components i.e. $\text{cov}(X, Y)$
Ultimately, rather than a single value, I'll have an entire matrix.

If have $n$ data points, $\mathbf{x}^i \in \mathbb{R}^d$, the data matrix $D$ would be an $n \times d$ matrix:

$$D = \begin{bmatrix}
 & x_{1}^{1} & x_{1}^{2} & \cdots & x_{1}^{n} \\
 & x_{2}^{1} & x_{2}^{2} & \cdots & x_{2}^{n} \\
 & \vdots & \vdots & \ddots & \vdots \\
 & x_{d}^{1} & x_{d}^{2} & \cdots & x_{d}^{n}
\end{bmatrix}
$$

Where each column represent a data point. And $x_{j}^{i}$ refers to the $j$-th component of the $i$-th data point.

The covariance matrix $C$ is a square $d \times d$ matrix that summarizes the variance and covariance for all pairs of dimensions in the dataset.
- The diagonal elements contain the variances of each individual dimension.
- The off-diagonal elements contain the co-variances between pairs of dimensions.

$$C = \begin{bmatrix}
\operatorname{cov}(1,1) & \operatorname{cov}(1,2) & \cdots & \operatorname{cov}(1,d) \\
\operatorname{cov}(2,1) & \operatorname{cov}(2,2) & \cdots & \operatorname{cov}(2,d) \\
\vdots & \vdots & \ddots & \vdots \\
\operatorname{cov}(d,1) & \operatorname{cov}(d,2) & \cdots & \operatorname{cov}(d,d)
\end{bmatrix}
$$

Where each element $C_{j,k}$ is the covariance between the $j$-th and $k$-th dimensions.

For 2-dimensional data with components $X, Y$, the covariance matrix would be $\rightarrow$

$$C = \begin{bmatrix} \sigma_{X} & \operatorname{cov}(X, Y) \\ \operatorname{cov}(Y, X) & \sigma_{Y} \end{bmatrix}$$

>[!cite] Covariance matrix
>The covariance matrix is defined as $\rightarrow$
>
>$$C = \frac{1}{N}{\sum_{i=1}^{N} (\mathbf{x}^i - \boldsymbol{\mu})(\mathbf{x}^i - \boldsymbol{\mu})^{T}}$$
>Where $\mathbf{x}^i$ is a column vector denoting the $i$-th data point.
^def-covariance-matrix

The covariance matrix gives you a complete picture. It doesn't just give you one average spread; it tells you the spread of _each_ variable individually and also describes the relationship _between_ the variables, which defines the orientation and shape of the data.

>[!tip]
>- Covariance matrix is symmetric.  $C = C^{T}$
>- This is simply because: $\text{cov}(\mathbf{x}^{j},\mathbf{x}^{k})= \text{cov}(\mathbf{x}^{k},\mathbf{x}^{j})$

#### Deriving the Covariance matrix

$\mathbf{x}^i$ is a column vector representing a single data point. And $\mu$ is the mean vector.

$$\mathbf{x}_i = \begin{bmatrix}
x_{1}^{i}\\
x_{2}^{i} \\
\vdots \\
x_{d}^{i}
\end{bmatrix} \  \text{   } \mu = \begin{bmatrix}
\mu_1 \\
\mu_2 \\
\vdots \\
\mu_d
\end{bmatrix}
$$

And the deviation vector $(\mathbf{x}^i−\mu)$ for the $i$-th data point is a column vector.
I take the outer product of the deviation column vector and its transpose (row vector), to get the *deviation matrix* $M_{i}$.
$$\begin{align} M_{i} &= (\mathbf{x}^i - \mu)(\mathbf{x}^i - \mu)^T = 
    \begin{bmatrix}
    (x_{1}^{i} - \mu_1) \\
    (x_{2}^{i} - \mu_2) \\
    \vdots \\
    (x_{d}^{i} - \mu_d)
    \end{bmatrix}
    \begin{bmatrix}
    (x_{1}^{i} - \mu_1) & (x_{2}^{i} - \mu_2) & \cdots & (x_{d}^{i} - \mu_d)
    \end{bmatrix}  
    \\
    \\ M_{i} &= 
    \begin{bmatrix}
    (x_{1}^{i} - \mu_1)(x_{1}^{i} - \mu_1) & (x_{1}^{i} - \mu_1)(x_{2}^{i} - \mu_2) & \cdots & (x_{1}^{i} - \mu_1)(x_{d}^{i} - \mu_d) \\
    (x_{2}^{i} - \mu_2)(x_{1}^{i} - \mu_1) & (x_{2}^{i} - \mu_2)(x_{2}^{i} - \mu_2) & \cdots & (x_{2}^{i} - \mu_2)(x_{d}^{i} - \mu_d) \\
    \vdots & \vdots & \ddots & \vdots \\
    (x_{d}^{i} - \mu_d)(x_{1}^{i} - \mu_1) & (x_{d}^{i} - \mu_d)(x_{2}^{i} - \mu_2) & \cdots & (x_{d}^{i} - \mu_d)(x_{d}^{i} - \mu_d)
\end{bmatrix}
\end{align}
$$


>[!tip] What is this $M$ ?
> Each data point $\mathbf{x}^i$ has a corresponding deviation matrix $M_i$. Every entry $(j,k)$ in $M_i$ is the *product of the j-th and k-th deviations from the mean, for that one data point.*
> $M_i$ as the **"covariance snapshot" contributed by a single data point**:​
> - The **diagonal entries** $(j,j)$ hold the squared deviation of dimension $j$ — that point's individual contribution to each dimension's variance.
> - The **off-diagonal entries** $(j,k)$ hold the cross-deviation products — that point's contribution to the covariance between dimensions $j$ and $k$.
> 
>Each *$M_i$ is a rank-1 matrix* encoding everything one data point contributes to the overall shape and spread of the data.


For each data-point I calculate the deviation matrix, for $\mathbf{x}^{1}$, I've $M_{1}$ and for $\mathbf{x}^{2}$, I've $M_{2}$ and so on. The covariance matrix $C$ is simply the **average of all $N$ deviation matrices.**

$$\begin{align} C & =\frac{1}{N}\Big[M_{1} + M_{2} + \cdots + M_{N}\Big] \\ \\ & =  \frac{1}{N}\Big[(\mathbf{x}^1 - \mu)(\mathbf{x}^1 - \mu)^{T} + (\mathbf{x}^2 - \mu)(\mathbf{x}^2 - \mu)^{T} + \cdots + (\mathbf{x}^N - \mu)(\mathbf{x}^N - \mu)^{T}\Big] \\ \\ & =  \frac{1}{N}\sum_{i=1}^{N}(\mathbf{x}^i - \mu)(\mathbf{x}^i - \mu)^{T} \end{align} $$

>[!warning] Think mf think
>I do the [[Measures of Variability#^def-covariance|same thing when finding co-variance]].
>- $\lambda_{i} = (x^i - \mu_x)(y^i - \mu_y)$ gives the co-variance contributed by the $i$-the data-points $(x^i,y^i)$.
>- Each data point contributes $\lambda_i$ co-variance, and I average these together to get the co-variance between $X, Y$. $$\text{cov}(X, Y) = \frac{1}{N}\sum_{i=1}^{N}\lambda_i$$

#### Determinant of the Co-variance Matrix

The determinant of a matrix encodes how much a linear transformation **scales volumes**. For a $2 \times 2$ matrix, it gives the area of the parallelogram formed by the column vectors; for a $d \times d$ matrix, it gives the $d$-dimensional volume of the parallelepiped formed by the columns. Geometrically, if I apply the matrix as a transformation to the unit cube, *the determinant tells me how much the volume got stretched or squished*. A determinant of $0$ means the transformation collapses the space into a lower dimension and that the columns are linearly dependent.

The determinant of the covariance matrix $C$ is called the **generalized variance**. It captures the **total spread of the data cloud as a single volume-like scalar**:
- A **large determinant** means the data is spread out widely across multiple dimensions, i.e the data cloud occupies a large volume.
- A **small determinant** means the data is compressed, either the individual variances are small, or the variables are highly correlated with each other.
- A **determinant near zero** means at least one variable is nearly a linear combination of the others, i.e., the data is essentially flat in some direction.

###### Correlation Shrinks the Determinant

Consider the 2-D covariance matrix:
$$C = \begin{bmatrix} \sigma_X^2 & \sigma_{XY} \\ \sigma_{XY} & \sigma_Y^2 \end{bmatrix}$$

Its determinant is: $\det(C) = \sigma_X^2 \sigma_Y^2 - \sigma_{XY}^2$
- When there is no co-relation the determinant is nothing but the full product of the individual variances. $\sigma_{XY} = 0$ and $\det(C) = \sigma_X^2 \sigma_Y^2$.
- But as correlation grows, the $\sigma_{XY}^2$ term eats into this, shrinking the determinant.
- At perfect correlation, $\det(C) = 0$, the data collapses to a 1-D line.

So the determinant is a measure of how much **independent spread** exists across all dimensions simultaneously, penalized by redundancy from correlations.

###### Trace of the Covariance Matrix

The [[Covariance Matrix#^def-multi-variate-cariance|multi-variate variance]] is actually the **trace** of the covariance matrix. $$\sigma = \frac{1}{N}{\sum_{i=1}^{N}\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}} = \text{trace}(C)$$
It only ever sees the diagonal of $C$, the off-diagonal covariance terms $\sigma_{XY}$ are completely invisible to it. This *trace tells me the total spread along axes*.

Consider two datasets, both with $\sigma_X^2 = \sigma_Y^2 = 1$:
- Dataset A: with $\sigma_{XY} = 0$, has $\text{trace}(C) = 2$ and $\det(C) = 1 \cdot 1 - 0 = 1$ (circular cloud)
- Dataset B: with $\sigma_{XY} = 0.99$, has $\text{trace}(C) = 2$ and $\det(C) = 1 - 0.98 = 0.02$ (thin ellipse nearly collapsed to a line)

Both have the same trace of 2 (the average squared deviation is identical). So just with trace I cannot tell them apart. However, the determinant correctly flags that Dataset B has almost no independent 2-D spread.

>[!danger]
>The determinant is still a **single scalar** though, so it also loses information. I can know the volume but I can't reconstruct the shape or orientation of the data from it alone
