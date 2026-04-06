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
>- Each data point $\mathbf{x}^{i}$ is a vector. The mean $\boldsymbol{\mu}$ is also a vector.
>- The term $\mathbf{x}^{i} - \boldsymbol{\mu}$ is the **deviation vector**, showing how far the point is from the center in each dimension.
>- And $\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}$ is the square of the L2 norm of the deviation vector.
>- Average all these and I have my variance. $\rightarrow$
>
> $$\sigma^2 = \frac{1}{N}{\sum_{i=1}^{N}\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}}$$
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

**Variance alone doesn't tell the whole story, it captures how spread out data is, but not the direction or shape of that spread.**

To fully describe a dataset, I need two things:
- The variance of each individual component, $\sigma^2_{X}$
- How each component _relates_ to the others, $\text{Cov}(X, Y)$


Lets say I have some 2-dimensional dataset, $\mathcal{D} = \{\mathbf{x}^i \mid \mathbf{x}^i \in \mathbb{R}^2\}_{i=1}^{N}$, with the two dimensions represented as two random variables $X_1$ and $X_2$.
A natural question is: *What is the combined variance of both variables together?*

The combined variance $\operatorname{Var}(X_1, X_2)$ can be expanded as,
$$\operatorname{Var}(X_1 + X_2) = \operatorname{Var}(X_1) + \operatorname{Var}(X_2) + 2\operatorname{Cov}(X_1, X_2)$$
This $\operatorname{Var}(X_1, X_2)$ is a single number, that captures total spread. But  it quietly packages three distinct pieces of information - two variances and one covariance. Rather than leaving them bundled up, it's cleaner to organize them into a $2 \times 2$ matrix $C$:
$$  
C =  
\begin{bmatrix}  
\text{Var}(X_1) & \text{Cov}(X_1, X_2) \\  
\text{Cov}(X_1, X_2) & \text{Var}(X_2)  
\end{bmatrix}  
$$
Further more, I can actually verify that $\text{Var}(X_1 + X_2)$ is exactly the **quadratic form** $\mathbf{1}^T C \mathbf{1}$ where $\mathbf{1} = \begin{bmatrix}1 & 1\end{bmatrix}^\top$:
$$  
\text{Var}(X_1 + X_2)  
=  
\begin{bmatrix}  
1 & 1  
\end{bmatrix}  
\begin{bmatrix}  
\text{Var}(X_1) & \text{Cov}(X_1, X_2) \\  
\text{Cov}(X_1, X_2) & \text{Var}(X_2)  
\end{bmatrix}  
\begin{bmatrix}  
1 \\  
1  
\end{bmatrix}  
$$
$C$ isn't some matrix that I assumed or pulled out of thin air, it fell out naturally as *the object that computes combined variance*. This $C$ is the **covariance matrix**.

>[!tip] Covariance matrix is not notation
>The *combined variance $\operatorname{Var}(X_1, X_2)$ is nothing but the sum of all the elements of the covariance matrix.*
>The covariance matrix $C$ is significant because it is the **natural object that computes combined variance**.
>Mathematically, the sum of all elements in a matrix can be expressed as the **quadratic form** $\mathbf{1}^T C \mathbf{1}$, where $\mathbf{1}$ is a vector of ones. This identity shows that the covariance matrix isn't just a way to organize data; **it is a structural representation of the system's total variability.**
>
>The covariance matrix gives me a complete picture. It doesn't just give me one average spread.
>- It tells me the **spread of each variable individually**.
>- And also describes the relationship between the variables, which **defines the orientation and shape of the data.**

#### Deriving the Covariance matrix

######  The Probabilistic Approach

>[!cite] Covariance Matrix Probabilistic definition
>The covariance matrix is formally defined for a random vector $\mathbf{X}=[X_1, X_2, \ldots, X_d]^\top$ as:​ $$C_\mathbf{X}=E\Big[(\mathbf{X}−E[\mathbf{X}]) \ (\mathbf{X}−E[\mathbf{X}])^\top\Big]$$
^def-covariance-matrix-probabilistic

I have a dataset,  $\mathcal{D} = \{\mathbf{x}^{i} \mid \mathbf{x}^{i} \in \mathbb{R}^d\}_{i=1}^{n}$, and each of the $d$ dimensions is represented its own random variable, i.e. $X_k$ is the random variable representing the $k$-th dimension. I can define a random vector $\mathbf{X} \in \mathbb{R}^d$ as:
$$
\mathbf{X} =
\begin{bmatrix}
X_1 \\
X_2 \\
\vdots \\
X_d
\end{bmatrix}
$$
Each observed data point $\mathbf{x}^{i}$ is one **realization** of this random vector, a single observed sample from the joint distribution of $(X_1, X_2, \ldots, X_d)$.

Again, I can ask the same question, *what is the combined variance of all variables together ?*
$$  
\operatorname{Var}(X_1 + X_2 + \cdots + X_d)  
=  
\operatorname{Var}\!\left(\sum_{j=1}^{d} X_j\right)  
$$
And using bi-linearity of covariance, I can expand this as:

$$  
\begin{align} \operatorname{Var}\!\left(\sum_{j=1}^{d} X_j\right)  
&=  
\sum_{j=1}^{d}\sum_{k=1}^{d} \operatorname{Cov}(X_j, X_k) \\  \\ &= \sum_{j=1}^{d}\Big[ \operatorname{Cov}(X_j, X_1) + \operatorname{Cov}(X_j, X_2) + \ldots + \operatorname{Cov}(X_j, X_d) \Big]\end{align}  
$$  
Writing this out explicitly, shows the pattern,
$$\begin{align} =\quad   [ & \operatorname{Cov}(X_1, X_1) + \operatorname{Cov}(X_1, X_2) + \ldots + \operatorname{Cov}(X_1, X_d) ] \\ + [& \operatorname{Cov}(X_2, X_1) + \operatorname{Cov}(X_2, X_2) + \ldots + \operatorname{Cov}(X_2, X_d) ] \\ + [ & \ldots ] \\ + [& \operatorname{Cov}(X_d, X_1) + \operatorname{Cov}(X_d, X_2) + \ldots + \operatorname{Cov}(X_d, X_d) ]\end{align}
$$
The double summation forms a $d \times d$ grid. And I define the covariance matrix $C$ with entries:
$$  
C =  
\begin{bmatrix}  
\operatorname{Var}(X_1) & \operatorname{Cov}(X_1,X_2) & \cdots & \operatorname{Cov}(X_1,X_d) \\  
\operatorname{Cov}(X_2,X_1) & \operatorname{Var}(X_2) & \cdots & \operatorname{Cov}(X_2,X_d) \\  
\vdots & \vdots & \ddots & \vdots \\  
\operatorname{Cov}(X_d,X_1) & \operatorname{Cov}(X_d,X_2) & \cdots & \operatorname{Var}(X_d)  
\end{bmatrix}  
$$

where, $C_{jk} = \operatorname{Cov}(X_j, X_k)$.

I can still verify the quadratic form, let $\mathbf{1} = [1, 1, \ldots, 1]^T \in \mathbb{R}^d$, then:
$$  
\begin{align} \operatorname{Var}(X_1 + \cdots + X_d) 
&=  
\sum_{j=1}^{d}\sum_{k=1}^{d}  
\operatorname{Cov}(X_j, X_k) 
\\ \\&=  \sum_{j=1}^{d}\sum_{k=1}^{d} 1 \cdot C_{jk} \cdot 1  
\\ \\&= \mathbf{1}^T C\, \mathbf{1} \end{align} 
$$  
The covariance matrix $C$ is a square $d \times d$ matrix that summarizes the variance and covariance for all pairs of dimensions in the dataset.
- The diagonal elements contain the variances of each individual dimension.
- The off-diagonal elements contain the co-variances between pairs of dimensions.
$$C = \begin{bmatrix}
\operatorname{Cov}(1,1) & \operatorname{Cov}(1,2) & \cdots & \operatorname{Cov}(1,d) \\
\operatorname{Cov}(2,1) & \operatorname{Cov}(2,2) & \cdots & \operatorname{Cov}(2,d) \\
\vdots & \vdots & \ddots & \vdots \\
\operatorname{Cov}(d,1) & \operatorname{Cov}(d,2) & \cdots & \operatorname{Cov}(d,d)
\end{bmatrix}
$$

Where each element $C_{jk}$ is the covariance between the $j$-th and $k$-th dimensions.

###### Deviation Vector Approach

>[!cite] Covariance matrix
>The covariance matrix is defined as,
>
>$$C = \frac{1}{N}{\sum_{i=1}^{N} (\mathbf{x}^i - \boldsymbol{\mu})(\mathbf{x}^i - \boldsymbol{\mu})^{T}}$$
>Where $\mathbf{x}^i$ is a column vector denoting the $i$-th data point. And $\boldsymbol{\mu} = \frac{1}{N}\sum_{i=1}^{N} \mathbf{x}^i$ is the mean.
^def-covariance-matrix

$\mathbf{x}^i$ is a column vector representing a single data point. And $\mu$ is the mean vector.

$$\mathbf{x}^i = \begin{bmatrix}
x_{1}^{i}\\
x_{2}^{i} \\
\vdots \\
x_{d}^{i}
\end{bmatrix} \quad \boldsymbol{\mu} = \begin{bmatrix}
\mu_1 \\
\mu_2 \\
\vdots \\
\mu_d
\end{bmatrix}
$$

And the **deviation vector** $(\mathbf{x}^i−\boldsymbol{\mu})$ for the $i$-th data point is a column vector.
I take the outer product of the deviation column vector and its transpose (row vector), to get the *deviation matrix* $M^{i}$.
$$\begin{align} M^{i} &= (\mathbf{x}^i - \boldsymbol{\mu})(\mathbf{x}^i - \boldsymbol{\mu})^T \\ \\ &= 
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
    \\ &= 
    \begin{bmatrix}
    (x_{1}^{i} - \mu_1)(x_{1}^{i} - \mu_1) & (x_{1}^{i} - \mu_1)(x_{2}^{i} - \mu_2) & \cdots & (x_{1}^{i} - \mu_1)(x_{d}^{i} - \mu_d) \\
    (x_{2}^{i} - \mu_2)(x_{1}^{i} - \mu_1) & (x_{2}^{i} - \mu_2)(x_{2}^{i} - \mu_2) & \cdots & (x_{2}^{i} - \mu_2)(x_{d}^{i} - \mu_d) \\
    \vdots & \vdots & \ddots & \vdots \\
    (x_{d}^{i} - \mu_d)(x_{1}^{i} - \mu_1) & (x_{d}^{i} - \mu_d)(x_{2}^{i} - \mu_2) & \cdots & (x_{d}^{i} - \mu_d)(x_{d}^{i} - \mu_d)
\end{bmatrix}
\end{align}
$$


>[!tip] What is this $M$ ?
> Each data point $\mathbf{x}^i$ has a corresponding deviation matrix $M^i$. Every entry $(j,k)$ in $M^i$ is the *product of the j-th and k-th deviations from the mean, for that one data point.*
> $M^i$ as the **"covariance snapshot" contributed by a single data point**:​
> - The **diagonal entries** $(j,j)$ hold the squared deviation of dimension $j$ — that point's individual contribution to each dimension's variance.
> - The **off-diagonal entries** $(j,k)$ hold the cross-deviation products — that point's contribution to the covariance between dimensions $j$ and $k$.
> 
>Each *$M^i$ is a rank-1 matrix* encoding everything one data point contributes to the overall shape and spread of the data.

For each data-point I calculate the deviation matrix, for $\mathbf{x}^{1}$, I've $M^{1}$ and for $\mathbf{x}^{2}$, I've $M^{2}$ and so on. The covariance matrix $C$ is simply the **average of all $N$ deviation matrices.**
$$\begin{align} C & =\frac{1}{N}\Big[M^{1} + M^{2} + \cdots + M^{N}\Big] \\ \\ & =  \frac{1}{N}\Big[(\mathbf{x}^1 - \mu)(\mathbf{x}^1 - \mu)^{T} + (\mathbf{x}^2 - \mu)(\mathbf{x}^2 - \mu)^{T} + \cdots + (\mathbf{x}^N - \mu)(\mathbf{x}^N - \mu)^{T}\Big] \\ \\ & =  \frac{1}{N}\sum_{i=1}^{N}(\mathbf{x}^i - \mu)(\mathbf{x}^i - \mu)^{T} \end{align} $$

>[!warning] Think mf think
>I do the [[Measures of Variability#^def-covariance|same thing when finding co-variance]].
>- $\lambda_{i} = (x^i - \mu_x)(y^i - \mu_y)$ gives the co-variance contributed by the $i$-the data-points $(x^i,y^i)$.
>- Each data point contributes $\lambda_i$ co-variance, and I average these together to get the co-variance between $X, Y$. $$\text{Cov}(X, Y) = \frac{1}{N}\sum_{i=1}^{N}\lambda_i$$

#### Determinant of the Co-variance Matrix

The determinant of a matrix encodes how much a linear transformation **scales volumes**. For a $2 \times 2$ matrix, it gives the area of the parallelogram formed by the column vectors; for a $d \times d$ matrix, it gives the $d$-dimensional volume of the parallelepiped formed by the columns. Geometrically, if I apply the matrix as a transformation to the unit cube, *the determinant tells me how much the volume got stretched or squished*. A determinant of $0$ means the transformation collapses the space into a lower dimension and that the columns are linearly dependent.

The determinant of the covariance matrix $C$ is called the **generalized variance**. It captures the **total spread of the data cloud as a single volume-like scalar**:
- A **large determinant** means the data is spread out widely across multiple dimensions, i.e the data cloud occupies a large volume.
- A **small determinant** means the data is compressed, either the individual variances are small, or the variables are highly correlated with each other.
- A **determinant near zero** means at least one variable is nearly a linear combination of the others, i.e., the data is essentially flat in some direction.

###### Correlation Shrinks the Determinant

Consider the 2-D covariance matrix:
$$C = \begin{bmatrix} \sigma_X^2 & \sigma_{XY} \\ \sigma_{XY} & \sigma_Y^2 \end{bmatrix}$$

Its determinant is: $\det(C) = \sigma_X^2 \sigma_Y^2 - (\sigma_{XY})^2$
- When there is no co-relation the determinant is nothing but the full product of the individual variances. $\sigma_{XY} = 0$ and $\det(C) = \sigma_X^2 \sigma_Y^2$.
- But as correlation grows, the $(\sigma_{XY})^2$ term eats into this, shrinking the determinant.
- At perfect correlation, $\det(C) = 0$, the data collapses to a 1-D line.

So the determinant is a measure of how much **independent spread** exists across all dimensions simultaneously, penalized by redundancy from correlations.

###### Trace of the Covariance Matrix

The [[Covariance Matrix#^def-multi-variate-cariance|multi-variate variance]] is actually the **trace** of the covariance matrix. $$\sigma^2 = \frac{1}{N}{\sum_{i=1}^{N}\| \mathbf{x}^{i} - \boldsymbol{\mu} \|^{2}_{2}} = \text{trace}(C)$$
It only ever sees the diagonal of $C$, the off-diagonal covariance terms $\sigma_{XY}$ are completely invisible to it. This *trace tells me the total spread along axes*.

Summing all elements (including off-diagonals) of the covariance matrix i.e. $\operatorname{Var}\left(\sum_{i=1}^{N}X_i\right) = \mathbf{1}^\top C  \mathbf{1}$, incorporates the **orientation and shape of the data created by correlations.** For instance, *if variables are highly correlated, the sum of all elements will be significantly larger than the trace, reflecting the "stretched" nature of the data cloud.*

Consider two datasets, both with $\sigma_X^2 = \sigma_Y^2 = 1$:
- Dataset A: with $\sigma_{XY} = 0$, has $\text{trace}(C) = 2$ and $\det(C) = 1 \cdot 1 - 0 = 1$ (circular cloud)
- Dataset B: with $\sigma_{XY} = 0.99$, has $\text{trace}(C) = 2$ and $\det(C) = 1 - 0.98 = 0.02$ (thin ellipse nearly collapsed to a line)

Both have the same trace of $2$ (the average squared deviation is identical). So just with trace I cannot tell them apart. However, the determinant correctly flags that Dataset B has almost no independent 2-D spread.

>[!danger]
>The determinant is still a **single scalar** though, so it also loses information. I can know the volume but I can't reconstruct the shape or orientation of the data from it alone

## Covariance Matrix Properties

#### Basic Properties

###### Square and Symmetric

$C$ is always symmetric matrix because there are exactly $d$ variables, and $C_{jk} = \operatorname{Cov}(X_j, X_k) = \operatorname{Cov}(X_k, X_j) = C_{kj}$.

This means $C$ lives in the same space as the data, I apply it as a linear transformation to vectors in $\mathbb{R}^d$.
Symmetry unlocks the **Spectral Theorem**, guaranteeing that $C$ has a complete set of real, *orthogonal eigenvectors*. This is what makes PCA possible.

######  Diagonal & Off-Diagonal Entries

$$C_{jj} = \operatorname{Cov}(X_j, X_j) = \operatorname{Var}(X_j) \geq 0$$
All diagonal entries variances, which implies they are non-negative. A zero diagonal entry means the variable has no variance, it is constant.

$$C_{jk} = \operatorname{Cov}(X_j, X_k) \quad \text{for} \quad j \neq k$$
The sign of $C_{jk}$ tells me whether the linear relation ship between $X_j$ and $X_k$ is positive, negative, or absent.

#### The core property: Positive Semi-Definiteness (PSD)

###### Real Non-negative Eigenvalues

For any weight vector $\mathbf{a} = [a_1, a_2, \ldots, a_d]^T$, I can show:
$$  
\operatorname{Var}(a_1 X_1 + \cdots + a_d X_d)  
=  
\sum_{j=1}^{d}\sum_{k=1}^{d}  
a_j\, C_{jk}\, a_k  
=  
\mathbf{a}^T C\, \mathbf{a}  
$$
The covariance matrix $C$ is the single object that computes the variance of **any linear combination** of my $d$ variables.

- $C$ is symmetric so it has **real eigenvalues**.
- $\mathbf{a}^T C\, \mathbf{a}=\operatorname{Var}(\mathbf{a}^T \mathbf{X})\geq 0$ implies $C$ **cannot have negative eigenvalues**. The data can collapse (flat directions), but cannot invert.
- For the $j$-th feature if If any eigenvalue $\lambda_j = 0$, then $C$ is **singular (non-invertible).**
-  $C$ is positive definite (all $\lambda_j > 0$) only when all dimensions have independent variation.

###### Spectral Decomposition

$$
C = Q \Lambda Q^T,
\quad Q^T Q = I
$$

- Columns of $Q$ = eigenvectors (principal directions)
- $\Lambda = \operatorname{diag}(\lambda_1, \ldots, \lambda_d)$
- **Eigenvalues = variance along each direction**
This is the geometric core of PCA.

###### Trace = Total Variance

$$
\operatorname{trace}(C)
=
\sum_{j=1}^{d} \lambda_j
=
\sum_{j=1}^{d} \operatorname{Var}(X_j)
$$

Total variance is invariant under rotation.

###### Determinant = Generalized Variance

$$
\det(C)
=
\prod_{j=1}^{d} \lambda_j
$$

- $\det(C) = 0 \Longleftrightarrow$  at least one $\lambda_j = 0$
- Measures volume of the data ellipsoid.
- High correlation shrinks this volume.

#### Transformation Properties

###### Linear Transformation $\mathbf{Y} = A\mathbf{X}$

For a random vector $\mathbf{X} \in \mathbb{R}^d$, where $X_k$ is the random variable representing the $k$-th dimension. the linear transformation $\mathbf{Y} = A \mathbf{X}$, the new covariance matrix is $C_Y$ is a quadratic function of $C_X$.
$$
C_Y = A\, C_X\, A^T
$$

This, governs how covariance changes under projections, rotations, scaling, which is central to PCA.

###### Translation In variance

As both variance and covariance are translation invariant.
- $\operatorname{Var}(X_k + b) = \operatorname{Var}(X_k)$
- $\operatorname{Cov}(X_j+b, X_k+c) = \operatorname{Cov}(X_j, X_k)$
 $$\mathbf{Y} = \mathbf{X} + \mathbf{b} \implies C_Y = C_X$$
This means **Covariance measures spread, not location**.

###### Not scale in-variant

Both variance and covariance are not scale-invariant
- $\operatorname{Var}(\alpha X_j) = \alpha^2 \operatorname{Var}(X_j)$
- $\operatorname{Cov}(\alpha X_j, \beta X_k) = \alpha \beta \operatorname{Cov}(X_j, X_k)$

**$C$ is not scale-invariant, feature normalization matters.**

#### Summary

| Property              | Statement                          | Key Consequence              |
| --------------------- | ---------------------------------- | ---------------------------- |
| Square                | $d \times d$                       | Linear map on $\mathbb{R}^d$ |
| Symmetric             | $C = C^T$                          | Spectral theorem applies     |
| Diagonal = Variances  | $C_{jj} \geq 0$                    | Non-negative diagonal        |
| PSD                   | $\mathbf{a}^T C \mathbf{a} \geq 0$ | No negative eigenvalues      |
| Eigenvalues           | $\lambda_j \geq 0$                 | Zero ⇒ degenerate direction  |
| Trace                 | $\sum \lambda_j$                   | Total variance               |
| Determinant           | $\prod \lambda_j$                  | Volume of data               |
| Linear transform      | $C_Y = A C_X A^T$                  | PCA, whitening               |
| Translation invariant | $C_{X+b} = C_X$                    | Only spread matters          |
| Not scale-invariant   | Quadratic scaling                  | Normalize features           |
| Cholesky              | $C = LL^T$                         | Sampling Gaussians           |
