---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Measures of Variability]]

---

## Variance for multi-variate data

For simple, uni-variate data (data with only one variable), [[Measures of Variability#^def-variance|standard deviation]] works perfectly. It gives me a single number representative of the average spread of the data.

A larger standard deviation means the data points are much more spread out from the mean, and a smaller standard deviation means the data points are clustered together around the mean.

The problem arises when I have **multivariate** data.

I can still define variance for multi-variate data, as the mean squared [[Norm#^def-euclidean-distance|Euclidean distance]] from the mean of the data cloud.

>[!cite] Multi-variate variance
>- Each data point $\mathbf{x}_{i}$ is a vector, The mean $\boldsymbol{\mu}$ is also a vector.
>- The term $\mathbf{x}_{i} - \boldsymbol{\mu}$ is the **deviation vector**, showing how far the point is from the center in each dimension.
>- And $\| \mathbf{x}_{i} - \boldsymbol{\mu} \|^{2}_{2}$ is the square of the L2 norm of the deviation vector.
>- Average all these and I have my variance. $\rightarrow$
>
> $$\sigma = \frac{1}{N}{\sum_{i=1}^{N}\| \mathbf{x}_{i} - \boldsymbol{\mu} \|^{2}_{2}}$$
>
> This single value is a perfectly valid *measure of the overall spread*. This gives me a single number that represents, on average, how far the data points are from the central mean, treating all directions of spread equally.

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

###### Deriving the generalized notation for the Covariance matrix

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

The covariance between the 1st and the 2nd component is given by $\rightarrow$

$$\text{cov}(1, 2) = \frac{1}{N}{\sum_{i=1}^{N}(x_{1}^{i} - \mu_{1})(x_{2}^{i} - \mu_{2})}$$

Where $x_{1}^{i}$ is the 1st component of the $i$-th data point.

For each data-point I calculate the deviation matrix, for $\mathbf{x}^{1}$, I've $M_{1}$ and for $\mathbf{x}^{2}$, I've $M_{2}$ and so on. To get the covariance matrix I can just add up all the deviation matrices and then finally divide by $N$.

$$\begin{align} C & =\frac{1}{N}\Big[M_{1} + M_{2} + \cdots + M_{N}\Big] \\ \\ & =  \frac{1}{N}\Big[(\mathbf{x}^1 - \mu)(\mathbf{x}^1 - \mu)^{T} + (\mathbf{x}^2 - \mu)(\mathbf{x}^2 - \mu)^{T} + \cdots + (\mathbf{x}^N - \mu)(\mathbf{x}^N - \mu)^{T}\Big] \\ \\ & =  \frac{1}{N}\sum_{i=1}^{N}(\mathbf{x}^i - \mu)(\mathbf{x}^i - \mu)^{T} \end{align} $$

*For each data point, compute the outer product of its deviation vector with itself. This creates a matrix of all pairwise products of deviations. Sum all these matrices together and average them. The result is the covariance matrix, where each element automatically contains the correct variance or covariance calculation.*

