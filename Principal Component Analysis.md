---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Features ain't Featuring

For the data-set $\mathcal{D} = \{\mathbf{x}^i \in \mathbb{R}^d\}_{i=i}^{n}$, where each sample (data-point) $\mathbf{x}^i$ is a $d$-dimensional vector. I have $d$ features, and my data lives in a $d$-dimensional space where **each axis corresponds to one feature**. Every data point is a location in that space and its coordinates are its feature values.
But here is the core problem: *just because a feature exists doesn't mean it's useful.*

###### Low Variance as redundancy

Let the variance and mean along the $j$-th dimension be $\sigma^2_j$ and $\mu_j$ respectively.
- If $\sigma^2_j \approx 0$, then $\mathbf{x}^i_j \approx \mu_j$ for every sample $i$. The feature is nearly constant, every sample has essentially the same value. I gain zero knowledge about a sample's identity by measuring it .
- If $\sigma^2_j$ is large, samples are spread far apart along that axis. Different samples have noticeably different values, making the feature useful for distinguishing them.

A feature with near zero variance is entirely redundant, it can be dropped with zero information loss. A feature with higher variance carries more potential discriminating power.
If every student in a class scores between $59$ and $61$ on a test, that test score tells me almost nothing about distinguishing one student from another.

**A feature that barely changes across my dataset is carrying almost no useful information.** It's dead weight.
*Throwing low variance features into a machine learning model adds noise, computation cost, and nothing else.*

A feature with near-zero variance corresponds to an axis where the entire data cloud is **pancake-thin** (squashed flat). The data doesn't spread along it. That axis is wasted space.

###### Correlation as redundancy

Now consider two features $j$ and $k$. Their covariance is: $$c_{jk} = \frac{1}{n}\sum_{i=1}^{n} (x^i_{j} - \mu_j)(x^i_{k} - \mu_k)$$
And the normalized version of covariance is the **Pearson correlation coefficient** (fancy way of saying co-relation):
$$r_{jk} =\frac{c_{jk}}{\sigma_j \sigma_k}, \qquad r_{jk} \in [-1, +1]$$

When $|r_{jk}| = 1$, features $j$ and $k$ are perfectly linearly dependent. One is just a scaled, shifted version of the other. *They occupy the same direction in feature space.* Keeping both is redundant. I'm paying the cost of two dimensions while only getting the information content of one.


If $|r_{jk}| \rightarrow 1$ then features $j$ and $k$ span only a **1-dimensional subspace**, not a **2-dimensional one**. Their **joint information content collapses to that of a single feature.**
Correlation between features creates **redundant dimensions**. Real datasets are full of this: Temperature and humidity tend to move together. A person's salary and their house size tend to move together. Pixel brightness in neighboring pixels of an image move together. None of these pairs are identical, but they overlap heavily. In all of these cases the features are not truly independent axes, they are oblique, overlapping representations of the same underlying signal.

#### Information Theory Perspective

>[!cite] Entropy
>In information theory, **entropy** $H$ measures how uncertain or surprising a random variable is. It is defined as the average amount of information I gain by observing the outcome of a random variable. The more unpredictable a variable is, the higher its entropy, the more information each observation gives me.
>
>For a continuous random variable $X$ with probability density function $p(x)$, the **differential entropy** is: $$H(X) = -\int_{-\infty}^{\infty} p(x) \ln p(x)\, dx$$
>This measures the average "surprise" per observation of $X$.
^def-entropy

>[!tip] Low variance is less information
>A constant random variable has entropy $H = 0$, it communicates nothing.
>For a continuous random variable $X$ following a Gaussian (normal) distribution with variance $\sigma_X^2$​, the differential entropy is defined as:$$H(X) = \frac{1}{2}\ln(2\pi e \sigma^2)$$
>The terms $2\pi$ and $e$ are just constants. The only variable is $\sigma^2$. Since $\ln$ is a strictly increasing function, entropy increases monotonically with variance.
>
>**So maximizing variance is, in a precise sense, maximizing entropy, thus maximizing information content.**


>[!cite] Shared Information Between Two Variables
> Given two random variables $X$ and $Y$, the **mutual information** $I(X;Y)$ measures how much knowing one reduces my uncertainty about the other: $$I(X;Y) = H(X) - H(X \mid Y)$$
> Here $H(X)$ is the information in $X$, and $H(X|Y)$ is the **conditional entropy**. It represents the amount of uncertainty or information still remaining in variable $X$ after I have already observed variable $Y$. The difference is the information $X, Y$ share.
> - If $X$ and $Y$ are completely independent: $H(X \mid Y) = H(X)$, so $I(X;Y) = 0$. Knowing $Y$ tells me nothing about $X$. **Zero redundancy.**
> - If $X$ and $Y$ are perfectly correlated: $H(X \mid Y) = 0$, so $I(X;Y) = H(X)$. Knowing $Y$ tells me everything about $X$. **Total redundancy.**
^def-mutual-entropy

>[!tip] Correlated Features are Redundant
> Conditional entropy $H(X|Y)$ represents the uncertainty remaining in $X$ after $Y$ is known. When two Gaussian variables are correlated with a coefficient $r$, the "unexplained" or conditional variance of $X$ given $Y$ is reduced based on their linear dependence: $$\sigma_{X|Y}^2 = \sigma_X^2(1 - r^2)$$ Plugging this conditional variance back into the Gaussian entropy formula gives:$$H(X|Y) = \frac{1}{2}\ln(2\pi e \sigma_X^2(1 - r^2))$$
> Mutual information between the two variables is, $$\begin{align} I(X;Y) &= \left[ \frac{1}{2}\ln(2\pi e \sigma_X^2) \right] - \left[ \frac{1}{2}\ln(2\pi e \sigma_X^2(1 - r^2)) \right] \\ &= \frac{1}{2} \ln \left( \frac{1}{1 - r^2} \right) \\ &= -\frac{1}{2}\ln(1 - r^2)\end{align}$$
>
> As $|r| \to 1$, mutual information **blows up to negative infinity**. The two features share an infinite amount of information with each other, meaning one is completely predictable from the other. Keeping both is infinitely redundant.

Both problems can be stated as:
- **Problem 1: Low variance:** $H(X_j) \approx -\infty$. Feature $j$ has near-zero information content on its own. *Useless*
- **Problem 2: Correlation:** $I(X_j; X_k) \to H(X_j)$. Feature $k$ contains almost all of the same information as feature $j$. *Redundant*

The **total useful information** in my full $d$-dimensional dataset is not $\sum_j H(X_j)$, this sum double-counts all the shared information. The true unique information is closer to:
$$H(X_1, X_2, \ldots, X_d)=\sum_j H(X_j)-\sum_{j<k} I(X_j;X_k)+ \ldots
$$
This is the **chain rule of entropy**. Correlated features bloat $\sum_j H(X_j)$ without proportionally increasing $H(X_1,\ldots,X_d)$. I'm paying the cost of many dimensions while getting the information of far fewer.

#### What to do ?

###### Why Feature Selection Fails

I have concluded two things:
1. **Problem 1:** Some original feature axes are nearly useless, near-zero variance
2. **Problem 2:** Some original feature axes overlap, high mutual information, high correlation.

A naive solution to both problems is **feature selection**: *just drop the bad features.*

For Problem 1, low variance features, feature selection is perfectly adequate. I compute the variance of each feature, rank them, drop the ones below a threshold. If $\sigma_j \approx 0$ then fucking drop the $j$-th feature. Zero information lost, fewer dimensions. **Feature selection fully solves Problem 1.**

But feature selection fails for Problem 2.

>[!example] Problem 2 cant be solved that easily
>Consider a dataset with three features height (cm), weight (kg), and BMI.
>
|Person|Height|Weight|BMI|
|---|---|---|---|
|1|160|55|21.5|
|2|175|80|26.1|
|3|190|95|26.3|
|4|155|50|20.8|
>
> All three features have high variance, I wouldn't drop any of them by variance alone. But BMI is literally computed from height and weigh. The three features are highly correlated. I'm carrying a lot of redundant information.
>
> Lets say I drop BMI. Fine. But now: which single feature, height, weight, or any individual axis, best captures the combined information of all three?
> None of them individually do. The richest single summary of this data is some _combination_ like "overall body size", a direction that runs diagonally through the height-weight-BMI space, capturing the shared signal from all three simultaneously.
>*Just dropping a feature does not work.*
>

When two features are correlated, their data cloud is a **diagonal ellipse**. The diagonal direction carries most of the variance. The two original axes (features), horizontal and vertical, are both _oblique cuts_ through this ellipse. Feature selection picks one of the original axes (feature) and throws the other away. But neither of the two original features (oblique cuts) is the best 1-D summary of the data. The diagonal, the direction the ellipse actually stretches is better than both, and it's a **linear combination** of the two original features.

###### The Fundamental Problem

The actual problem is, that **the original axes (features) I had are not the right axes to describe my data.** They're the axes that the measuring instruments, or observations gave me, height, weight, temperature, pixel brightness. **What I need are axes that best describe the actual structure of the data.**

So, **what if I invented entirely new axes?**
Not the original feature axes. Not a subset of them. Brand new directions in the same space, that are linear combinations of the original features. Brand new directions that are specifically designed to be:
- **Maximally spread out** (high variance → solves Problem 1)
- **Completely uncorrelated with each other** (zero mutual information → solves Problem 2)

This is where Principal Component analysis comes in.
Unlike feature selection where I ask "which of my existing features should I keep?".
In principal component analysis I ask *"what are the best possible axes I could use to describe this data?"*

## Principal Component Analysis

Feature selection only deletes bad axes; it never constructs better ones.
PCA’s whole job is to _construct_ new axes that are -
- as informative as possible
- non‑redundant.

The original feature axes are bad because they have low variance and overlap with each other, so instead of keeping them, PCA finds a completely new set of axes (via projection) that are specifically optimized to have maximum variance and zero overlap.

I want new axes that best describe my data rather than using the original features (axes). A "new axis" in $\mathbb{R}^d$ is just a direction, a unit vector $\mathbf{p}$. Expressing a data point $\mathbf{x}^{i}$ in terms of this new axis means finding its coordinate along it, which is exactly the dot product $\mathbf{p}^\top \mathbf{x}^i$, nothing but the projection of $\mathbf{x}^{i}$ along the direction $\mathbf{p}$.

**So "finding the best new axes" and "finding the best projection directions" are the exact same thing, just stated differently.**

#### Formalizing PCA

Given a dataset $\mathcal{D} = \{\mathbf{x}^i \in \mathbb{R}^d\}_{i=i}^{n}$. Each point is $d$-dimensional. I want to transform this data into a $k$ dimensional space, i.e. project every point down to $k$ dimensions, where $k \ll d$, while losing as little information as possible.

Now let $\mathbf{p} \in \mathbb{R}^d$ be a $d$-dimensional vector. For any data point $\mathbf{x}^i$, the projection of $\mathbf{x}^i$ along $\mathbf{p}$ be $z^i$.
$$z^{i} = \mathbf{p}^{T} \mathbf{x}^i$$
Each $z^i$ is a scalar value that tells me: how far along the direction $\mathbf{p}$ the point $\mathbf{x}^i$ sits.
$$
z^i = p_1 x^i_1 + p_2 x^i_2 + \cdots + p_d x^i_d  
$$

I project every data point in my dataset, along $\mathbf{p}$. Each $x^i$ becomes a scalar $z^i$. The full dataset collapses to a simple 1-D vector $\mathbf{Z}$, in which all my original points are collapsed onto a single axis.
$$\mathbf{Z} = \{z^1, z^2, \ldots, z^n\}$$
I took all my data-points, which were floating in some high-dimensional space and **squashed** them onto a single line. Every point now just has one number describing it: its position along that line.

The direction of $\mathbf{p}$ matters, depending on what $\mathbf{p}$ I chose, the spread of $\mathbf{Z}$ would be entirely different.
![[pca-projection-p-matters.png]]


PCA's core question is: **which direction $\mathbf{p}$ should I pick?**
The answer: pick the direction that makes $\mathbf{Z}$ as spread out as possible. In other words, **maximize the variance of $\mathbf{Z}$**.

###### $\mathbf{p}$ has to be a unit vector

I want to maximize $\text{Var}(\mathbf{Z})$ over all choices of $\mathbf{p}$. That is,
$$\max_{\mathbf{p}}\text{Var}(\mathbf{Z})$$
The projection of a single point $\mathbf{x}^i$ onto direction $\mathbf{p}$ is $z^i = \mathbf{p}^{T} \mathbf{x}^i$, and if I were to double $\mathbf{p}$,
$$
\begin{align} z^{i'} &= (2\mathbf{p})^{T} \mathbf{x}^i  \\   
z^{i'} &= 2(\mathbf{p}^{T} \mathbf{x}^i) \\ z^{i'} &= 2z^i  \end{align}
$$
Every projection value just doubled. And variance scales by the square of the scaling factor.
$$ \begin{align} \text{Var}(\mathbf{Z}') &= \text{Var}(2\mathbf{Z})  \\  
\text{Var}(\mathbf{Z}') &= 4 \cdot \text{Var}(\mathbf{Z})   \end{align}
$$

I haven't changed the direction I'm projecting onto at all. Scaling $\mathbf{p}$ doesn't find a better direction. It just re-scales all the $z^i$ values upward, making the variance look bigger for no real geometric reason.

If I'm allowed this, the "optimal" $\mathbf{p}$ would always be $\mathbf{p} \rightarrow \infty$, making variance infinitely large. *This is retarded*. The optimization problem would have **no solution**.

I care about the direction of $\mathbf{p}$, not the exact scale of $\mathbf{p}$. So I impose a constraint, I make $\mathbf{p}$ scale in-variant, **$\mathbf{p}$ must be a unit vector**: $$\mathbf{p}^{T} \mathbf{p} = 1$$
Now, the optimization problem is well-posed:
$$
\max_{\mathbf{p}}\text{Var}(\mathbf{Z})
\quad
\text{subject to}
\quad
\mathbf{p}^{T}\mathbf{p} = 1
$$

#### Setting up the Optimization Problem

Let $\boldsymbol{\mu}$ be the mean of my dataset $\mathcal{D} = \{\mathbf{x}^i \in \mathbb{R}^d\}_{i=i}^{n}$.  And let $\bar{z}$ be the mean of $\mathbf{Z}$.

$$  
\begin{align} \bar{z} &=\frac{1}{n}\sum_{i=1}^{n} z^i \\ &= \frac{1}{n}\sum_{i=1}^{n} \mathbf{p}^{T} \mathbf{x}^i \\ &= \mathbf{p}^{T} \left(\frac{1}{n}\sum_{i=1}^{n}\mathbf{x}^i\right) \\ \bar{z} &=  \mathbf{p}^{T} \boldsymbol{\mu}\end{align}
$$ 
Let $v_{\mathbf{z}}$ be the variance of $\mathbf{Z}$,
$$  
\begin{align} v_{\mathbf{z}} &= \frac{1}{n}\sum_{i=1}^{n} (z^i - \bar{z})^2
\\ &= \frac{1}{n}\sum_{i=1}^{n} (\mathbf{p}^{T} \mathbf{x}^i - \mathbf{p}^{T} \boldsymbol{\mu})^2
\\ &= \frac{1}{n}\sum_{i=1}^{n} (\mathbf{p}^{T} \mathbf{x}^i - \mathbf{p}^{T} \boldsymbol{\mu})(\mathbf{p}^{T} \mathbf{x}^i - \mathbf{p}^{T} \boldsymbol{\mu})
\\ &= \frac{1}{n}\sum_{i=1}^{n} \mathbf{p}^{T}(\mathbf{x}^i - \boldsymbol{\mu})\mathbf{p}^{T}(\mathbf{x}^i - \boldsymbol{\mu})
\\ &= \frac{1}{n}\sum_{i=1}^{n} \mathbf{p}^{T}(\mathbf{x}^i - \boldsymbol{\mu}) (\mathbf{x}^i - \boldsymbol{\mu})^{T}\mathbf{p} \end{align}   
$$
Pull $\mathbf{p}$ outside the sum (since it does not depend on $i$):
$$  
v_{\mathbf{z}} =  \mathbf{p}^{T}\left[\frac{1}{n}\sum_{i=1}^{n}(\mathbf{x}^i - \boldsymbol{\mu})(\mathbf{x}^i - \boldsymbol{\mu})^{T}\right]\mathbf{p}  
$$

The summation within the square brackets in nothing but the **covariance matrix** $\mathbf{C}$.
$$
\mathbf{C} =  
\frac{1}{n}\sum_{i=1}^{n}  
(\mathbf{x}^i - \boldsymbol{\mu})  
(\mathbf{x}^i - \boldsymbol{\mu})^{T}  
$$
So the variance becomes
$$  
v_{\mathbf{z}} = \mathbf{p}^{T} \mathbf{C} \mathbf{p}  
$$

And the optimization reduces to:
$$  
\max_{\mathbf{p}}  
(\mathbf{p}^{T} \mathbf{C} \mathbf{p})  
\quad  
\text{subject to}  
\quad  
\mathbf{p}^{T} \mathbf{p} = 1  
$$  
This is a **constrained optimization problem**. I can't just take a gradient and set it to zero, I have a constraint that must always hold. The standard mathematical tool for this is the method of **Lagrange multipliers**.

###### Lagrange Multipliers

The standard Lagrangian form is:
$$  
\mathcal{L}(\mathbf{p},\alpha)=- f(\mathbf{p}) +
\alpha.h(\mathbf{p})
$$

I have two things:
- An **objective function** $f(\mathbf{p})$ that I want to maximize.
- A **constraint function** $h(\mathbf{p})$ that must be equal to zero.

In this particular case:
- $f(p) = \mathbf{p}^{T}\mathbf{C}\mathbf{p}$
- $h(p) = \mathbf{p}^{T} \mathbf{p} - 1$

The constraint $h(\mathbf{p}) = \mathbf{p}^\top \mathbf{p} - 1 = 0$, defines a *surface* (in this case specifically its a sphere of radius $1$). **My solution $\mathbf{p}$ must lie exactly on this surface. I cannot leave it**.

If I stand at point $\mathbf{p}$ and want to increase $f$ as fast as possible, I walk in the direction of the gradient $\nabla f(\mathbf{p})$, the gradient is always perpendicular to the contour lines. And a contour line is a curve along which $f$ stays constant. *The direction of steepest change (gradient) is always perpendicular to the direction of no change (contour lines).*

Similarly, $\nabla h(\mathbf{p})$ points *perpendicular to the constraint surface*. It points in the direction that would take me off the constraint surface fastest.

Since I must stay on the constraint surface, the only moves I'm allowed to make are **tangential** to the constraint surface. Any allowed movement direction $\mathbf{d}$ must satisfy $\nabla h(\mathbf{p})^\top \mathbf{d} = 0$. In other words, my movement direction must be **perpendicular to $\nabla h$**.

I move tangentially along the constraint surface and increase $f$. The rate of change of $f$ in direction $\mathbf{d}$ is: $\nabla f(\mathbf{p})^\top \mathbf{d}$
So the question becomes: Is there a tangential direction $\mathbf{d}$ satisfying $\nabla h(\mathbf{p})^\top \mathbf{d} = 0$ such that $\nabla f(\mathbf{p})^\top \mathbf{d} > 0$ ?

And *I get stuck at an optimum only when no such direction exists*. That means every direction that keeps me on the constraint surface either leaves $f$ unchanged or decreases it.

*When can there be no direction $\mathbf{d}$ that is both tangential to the surface and increases $f$?*
Only when $\nabla f(\mathbf{p})$ has **no component along the surface at all**. In other words, $\nabla f(\mathbf{p})$ points entirely **perpendicular to the constraint surface**. Because if $\nabla f$ had any tangential component, I could walk along that component and increase $f$ while staying on the surface.


So, $\nabla h$ points perpendicular to the constraint surface and $\nabla f$ must also be perpendicular to the constraint surface. **$\nabla f$ must be parallel to $\nabla h$**, that is for some scalar $\alpha$:
$$  
\nabla f(\mathbf{p}) = \alpha \, \nabla h(\mathbf{p})
$$This is the **Lagrange multiplier condition**.

###### Solve Using Lagrange Multipliers

For the optimization problem:
$$  
\max_{\mathbf{p}}
(\mathbf{p}^{T} \mathbf{C} \mathbf{p})
\quad
\text{subject to}
\quad
\mathbf{p}^{T} \mathbf{p} = 1
$$I actually want to maximize, but ML retards prefer to have optimizations that 'minimize'. So, going by the same retarded convention, instead of maximizing $(\mathbf{p}^{T} \mathbf{C} \mathbf{p})$, I minimize $- (\mathbf{p}^{T} \mathbf{C} \mathbf{p})$, the problem is restated as:
$$  
\min_{\mathbf{p}}
-(\mathbf{p}^{T} \mathbf{C} \mathbf{p})
\quad
\text{subject to}
\quad
\mathbf{p}^{T} \mathbf{p} = 1
$$
The **Lagrangian** for this optimization problem is:$$
\mathcal{L}(\mathbf{p},\alpha)=-\mathbf{p}^{T} \mathbf{C} \mathbf{p}+
\alpha(\mathbf{p}^{T} \mathbf{p} - 1)
$$The scalar $\alpha$ is the **Lagrange multiplier** enforcing the $\mathbf{p}^{T}\mathbf{p} = 1$ constraint. And at the optimal $\mathbf{p}$, the gradient of $\mathcal{L}$ with respect to $\mathbf{p}$ must be zero.

Using the property $\nabla_{X}(X^{T} A X)=(A+A^{T})X$ and because $\mathbf{C}$ is a symmetric matrix, the gradient $\nabla_{\mathbf{p}}f(\mathbf{p})$ is:
$$\begin{align} \nabla_{\mathbf{p}}f(\mathbf{p}) &= \nabla_{\mathbf{p}}(-\mathbf{p}^{T}\mathbf{C}\mathbf{p}) \\ &= -(\mathbf{C}^{T} + \mathbf{C})\mathbf{p} \\ &= -2\mathbf{C}\mathbf{p} \end{align}$$
Using the property $\nabla_{X}(X^{T}X)=2X$, the gradient $\nabla_{\mathbf{p}}h(\mathbf{p})$ is:
$$\begin{align} \nabla_{\mathbf{p}}h(\mathbf{p}) &= \nabla_{\mathbf{p}}(\mathbf{p}^{T} \mathbf{p} - 1) \\ &= 2\mathbf{p} \end{align}$$
Finally,
$$\begin{align} \nabla_{\mathbf{p}} \ \mathcal{L}(\mathbf{p}, \alpha) &= 0 \\ \\ -2\mathbf{C}\mathbf{p} + \alpha. (2 \mathbf{p}) &= 0 \\ \\ \mathbf{C}\mathbf{p} &= \alpha \mathbf{p} \end{align}$$
This $\mathbf{C}\mathbf{p} = \alpha \mathbf{p}$ is the **eigenvalue equation**. The optimal direction $\mathbf{p}$ is an eigenvector of the covariance matrix $\mathbf{C}$, and the Lagrange multiplier $\alpha$ is its corresponding eigenvalue.
The eigenvalue equation doesn't give me a single answer, it has **multiple valid solutions**.

Any eigenvector of $\mathbf{C}$ satisfies $\mathbf{C}\mathbf{p} = \alpha \mathbf{p}$ and since $\mathbf{C}$ is a $d \times d$ matrix, it has exactly $d$ eigenvectors $\mathbf{e}_1, \mathbf{e}_2, \ldots, \mathbf{e}_d$ with corresponding eigenvalues $\lambda_1, \lambda_2, \ldots, \lambda_d$

So the optimization has not one solution but $d$ candidate solutions. The question is: *Which one do I pick?*

###### Picking the Right Eigenvector

I was maximizing: $v_{\mathbf{z}} = \mathbf{p}^\top \mathbf{C} \mathbf{p}$. When $\mathbf{p}$ is an eigenvector, this becomes
$$
v_{\mathbf{z}} = \mathbf{p}^\top \mathbf{C} \mathbf{p}
= \mathbf{p}^\top (\alpha \mathbf{p})
= \alpha (\mathbf{p}^\top \mathbf{p})
$$
Since $\mathbf{p}^\top \mathbf{p} = 1$,
$$
v_{\mathbf{z}} = \alpha
$$
So the variance of the projections $\mathbf{Z}$, **equals the eigenvalue**.

Therefore, the question. "Which value of $\mathbf{p}$ gives maximum variance?" becomes "Which eigenvector $\mathbf{e}_{\text{max}}$ has the largest eigenvalue $\lambda_{\text{max}}$?"

I pick the eigenvector corresponding to $\lambda_{\max}$, the **largest eigenvalue** of $\mathbf{C}$. This is my **first principal component**, $\mathbf{e}_1$.
I use the direction $\mathbf{e}_1$, and my project dataset $\mathbf{Z}$ becomes:
$$\begin{align} z^i &= \mathbf{p}^\top \mathbf{x}^i \\ z^i &= \mathbf{e}_1^\top \mathbf{x}^i \\ \\ \mathbf{Z} &= \{z^i | z^i = \mathbf{e}_1^\top \mathbf{x}^i\}_{i=1}^{n} \end{align}$$

#### The Second Direction

I found the best single direction $\mathbf{p} = \mathbf{e}_1$. But I want to reduce from $d$ dimensions to $k$ dimensions, so I need a **second direction** $\mathbf{q}$ to project onto as well. I define a new projection, $u^{i}$ along the direction $\mathbf{q}$ for each $\mathbf{x}^{i}$, and a new 1-D vector $\mathbf{U}$ such that,
$$\begin{align} u^i &= \mathbf{q}^\top \mathbf{x}^i \\ \\ \mathbf{U} &= \{u^1, u^2, \ldots, u^n\} \end{align}$$

I can't just pick the second largest eigenvector blindly, I first need to decide what constraint $\mathbf{q}$ should satisfy. There are two requirements:

1. **Unit norm:** $\mathbf{q}^\top \mathbf{q} = 1$ same as before, to prevent trivial scaling
2. **Uncorrelated with the first projection:** the new projected dataset $\mathbf{U} = \{u^i | u^i = \mathbf{q}^\top \mathbf{x}^i\}_{i=1}^{n}$ must have zero covariance with $\mathbf{Z} = \{z^i | z^i = \mathbf{e}_1^\top \mathbf{x}^i\}_{i=1}^{n}$

I want the new direction $\mathbf{q}$ to have no co-relation with $\mathbf{e}_1$. I want the second component to carry **new information** the first one doesn't.

I want the correlation between $\mathbf{Z}$ and $\mathbf{U}$ to be zero. Or in other words, I want the covariance $\text{Cov}(Z, U)$ to be zero.

$$
\text{Cov}(Z, U) =
\frac{1}{n}\sum_{i=1}^{n}(z^i - \bar{z})(u^i - \bar{u})
$$
The mean of $\mathbf{Z}$ and $\mathbf{U}$ is,
$$
\begin{align} \bar{z} &= \frac{1}{n}\sum_{i=1}^n z^i = \frac{1}{n}\sum_{i=1}^n \mathbf{e}_1^\top \mathbf{x}^i = \mathbf{e}_1^\top \boldsymbol{\mu} \\ \bar{u} &= \frac{1}{n}\sum_{i=1}^n u^i = \frac{1}{n}\sum_{i=1}^n \mathbf{q}^\top \mathbf{x}^i = \mathbf{q}^\top \boldsymbol{\mu}\end{align}
$$
Substituting the values of $z^i, u^i, \bar{z}$ and $\bar{u}$,
$$
\begin{align} \text{Cov}(Z,U)
&=
\frac{1}{n}\sum_{i=1}^{n}
(\mathbf{e}_1^\top \mathbf{x}^i - \mathbf{e}_1^\top \boldsymbol{\mu})
(\mathbf{q}^\top \mathbf{x}^i - \mathbf{q}^\top \boldsymbol{\mu})
\\ \\
&=
\frac{1}{n}\sum_{i=1}^{n}
\mathbf{e}_1^\top(\mathbf{x}^i - \boldsymbol{\mu})
\cdot
\mathbf{q}^\top(\mathbf{x}^i - \boldsymbol{\mu})
\\ \\
&=
\frac{1}{n}\sum_{i=1}^{n}
\mathbf{e}_1^\top(\mathbf{x}^i - \boldsymbol{\mu})
(\mathbf{x}^i - \boldsymbol{\mu})^\top
\mathbf{q}
\\ \\
\text{Cov}(Z, U) &= \mathbf{e}_1^\top \mathbf{C} \, \mathbf{q}
\end{align}
$$
Substituting $\mathbf{C}\mathbf{e}_1 = \lambda_1 \mathbf{e}_1$, and doing some transpose trickery, I can re-write this as:
$$
\begin{align} \text{Cov}(Z, U) &= \mathbf{e}_1^\top \mathbf{C} \mathbf{q} = (\mathbf{C}\mathbf{e}_1)^\top \mathbf{q} = (\lambda_1 \mathbf{e}_1)^\top \mathbf{q} \\ \\ \text{Cov}(Z, U) &= \lambda_1 (\mathbf{e}_1^\top \mathbf{q}) \end{align}
$$

$\lambda_1$ is a positive scalar (variances are always positive, eigenvalues of a covariance matrix are always $\geq 0$, and the first one is the largest so it's definitely $> 0$). Therefore:

$$
\text{Cov}(Z, U) = 0
\iff
\mathbf{e}_1^\top \mathbf{q} = 0
$$

Basically, **for there to be zero covariance between $\mathbf{U}$ and $\mathbf{Z}$, the vector $\mathbf{q}$ must be orthogonal to $\mathbf{e}_1$.**

I have two constraint, unit norm ($\mathbf{q}^{T} \mathbf{q} = 1$) and orthogonality $\mathbf{e}_1^\top \mathbf{q} = 0$. Now, the new optimization problem for the second direction becomes,
$$  
\max_{\mathbf{q}}  
(\mathbf{q}^{T} \mathbf{C} \mathbf{q})  
\quad  
\text{subject to}  
\quad  
\mathbf{q}^{T} \mathbf{q} = 1 \text{  and  } \mathbf{e}_1^\top \mathbf{q} = 0
$$

###### Lagrangian for the second direction

The Lagrangian is

$$
\mathcal{L}(\mathbf{q}, \alpha, \beta)
=
-\mathbf{q}^\top \mathbf{C} \mathbf{q}
+
\alpha(\mathbf{q}^\top \mathbf{q} - 1)
+
\beta(\mathbf{q}^\top \mathbf{e}_1)
$$
Two constraints, two multipliers, $\alpha$ for unit norm, $\beta$ for orthogonality. Setting the gradient $\nabla_{\mathbf{q}}\mathcal{L}$ to zero :

$$
\begin{align} \nabla_{\mathbf{q}}\mathcal{L} = -2\mathbf{C}\mathbf{q} + 2\alpha \mathbf{q}+\beta \mathbf{e}_1 = \mathbf{0} \end{align}
$$

Here, $\beta = 0$. I can show this simply by pre-multiplying by $\mathbf{e}_1^\top$.
$$\\ \\ -2\mathbf{e}_1^\top \mathbf{C} \mathbf{q}+2\alpha (\mathbf{e}_1^\top \mathbf{q})+\beta (\mathbf{e}_1^\top \mathbf{e}_1)=0 $$
Because $\mathbf{e}_1^\top \mathbf{q} = 0$ and $\mathbf{e}_1^\top \mathbf{e}_1 = 1$, this reduces to,
$$
\beta = 2\mathbf{e}_1^\top \mathbf{C} \mathbf{q}
$$
The term $\mathbf{e}_1^\top \mathbf{C} \mathbf{q}$ is nothing but the $\text{Cov}(\mathbf{Z}, \mathbf{U})$, which is zero. Therefore $\beta=0$.

So the **equation simplifies to same eigenvalue equation again**,

$$
\begin{align} \nabla_{\mathbf{q}}\mathcal{L} &= -2\mathbf{C}\mathbf{q} + 2\alpha \mathbf{q}+\beta \mathbf{e}_1 = \mathbf{0} \\ \\  0 &= -2\mathbf{C}\mathbf{q} + 2\alpha \mathbf{q} \\ \\ \mathbf{C}\mathbf{q} &= \alpha \mathbf{q}\end{align}
$$
The second component is also an eigenvector of $\mathbf{C}$. To maximize variance, I pick the eigenvector $\mathbf{e}_2$ with the **second largest eigenvalue** $\lambda_2$.
And since *eigenvectors of a real symmetric matrix with distinct eigenvalues are always orthogonal*, $\mathbf{e}_1$ is perpendicular to $\mathbf{e}_2$, the orthogonality constraint is satisfied automatically.

#### Dimensionality Reduction Transformation

I can repeat this argument for a third direction, a fourth, and so on. Every time, the same eigenvalue equation emerges.

>[!success] Eigenvector of $\mathbf{C}$ is the principal component
>The **$k$-th principal component is the eigenvector of $\mathbf{C}$ corresponding to the $k$-th largest eigenvalue**. All principal components are mutually orthogonal and uncorrelated.
>**The variance captured along the $k$-th component is exactly $\lambda_k$.**

Every principal component is just an eigenvector of the covariance matrix, and the eigenvalues tell me exactly how much variance (how much information) each one carries.

###### Eigen-decompose $\mathbf{C}$

I compute all the all $d$ eigenvectors and eigenvalues of $\mathbf{C}$ and sort them in descending order: $\lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_d$
I can define two vectors $\Lambda$ and $\mathbf{E}$ as,
$$
\boldsymbol{\Lambda} = [\lambda_1, \ldots, \lambda_d]
\qquad
\mathbf{E} = [\mathbf{e}_1, \ldots, \mathbf{e}_d]
$$
Each eigenvalue's corresponding eigenvector gets sorted into $\mathbf{E}$ in the same order. At this point I have all $d$ principal components, ranked by how much variance each one carries.
Now the only decision I need to make is: **how many components to keep ?**

I define a parameter $s_m$, which is the **total variance captured** by the top $m$ features. And as the variance of the $k$-th feature is simply $\lambda_k$, $s_m$ can written as:
$$
s_m = \sum_{i=1}^{m} \lambda_i
$$

Another parameter $\rho_m$ called **explained variance ratio** can be defined which gives me the ratio of the variance contributed by the top $m$ features ($s_m$) and the total variance contributed by all the $d$ features ($s_d$).
$$
\rho_m =
\frac{s_m}{s_d}
=
\frac{\lambda_1 + \cdots + \lambda_m}
{\lambda_1 + \cdots + \lambda_m + \cdots + \lambda_d}
$$


I choose the smallest $k$ such that $\rho_k \geq \eta$ where $\eta$ is a threshold (typically $0.95$ or $0.99$). I'm finding the *minimum number of dimensions needed to preserve a sufficient fraction of variance.*

Now based on what fraction of variance I want to preserve, I split $\mathbf{E}$ into two parts; the first $k$ eigenvectors form the transformation matrix $\mathbf{Q}$.
$$
\mathbf{E}
=
\Big[\underbrace{\mathbf{e}_1, \mathbf{e}_2, \ldots, \mathbf{e}_k}_{\mathbf{Q}}
\;\Big|\;
\underbrace{\mathbf{e}_{k+1}, \mathbf{e}_{k+2}, \ldots, \mathbf{e}_d}_{\text{discard these}}\Big]
$$$$
\mathbf{Q}
=
[\mathbf{e}_1, \mathbf{e}_2, \ldots, \mathbf{e}_k]
\in \mathbb{R}^{d \times k}
$$

###### It was always zero-centered

Variance by definition is the average spread from the centroid (mean). And covariance matrix is just a generalization of that, *it defines the orientation and shape of the data from the centroid (mean).*
$$\mathbf{C} = \sum_{i=1}^{n} (\mathbf{x}^i - \boldsymbol{\mu}) (\mathbf{x}^i - \boldsymbol{\mu}) ^\top$$

Every single entry of $\mathbf{C}$ was built from $(\mathbf{x}^i - \boldsymbol{\mu})$ deviations. The mean subtraction is structurally woven into every element of $\mathbf{C}$. *There is no part of $\mathbf{C}$ that contains information about where the cloud is located. Only how it is shaped.* Covariance matrix is translation invariant, and thus its eigenvectors $\mathbf{e}_1, \mathbf{e}_2, \ldots, \mathbf{e}_k$ only encode shape and directions of spread. They implicitly assume they are describing a cloud centred at the mean.

 /if I want to project a new point $\mathbf{x}$, I must first move it into the eigenvectors' frame of reference by subtracting $\mu$, which is called **zero centering** the data point (bringing the data to the origin that the eigenvectors call home, before measuring its coordinates along them).
The final transformation for any data point $\mathbf{x}$ is:
$$
\mathbf{v} = \mathbf{Q}^\top (\mathbf{x} - \boldsymbol{\mu})
$$

Each entry $v_j$ is the dot product of the data point $x$ with the $j$-th principal component $\mathbf{e}_j$.
$\mathbf{Q}^\top(\mathbf{x} - \boldsymbol{\mu})$ does three things:
1. **Centering** $(\mathbf{x} - \boldsymbol{\mu})$
2. **Rotating** $(\mathbf{Q}^\top \cdot)$
3. **Projecting** onto top $k$ directions

The result $\mathbf{v}$ is the most informative possible $k$-dimensional representation of my original data.



