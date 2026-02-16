---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Estimation Theory]]

---

## Kernel Density Estimation

Kernel Density Estimation is a [[Estimation Theory#Non-Parametric Density Estimation|non-parametric]] statistical technique used to estimate the probability density function (PDF).
The fundamental idea behind KDE is that the more data points there are in a specific region, the higher the probability of a new observation occurring there. The process works by:​
1. Placing a [[Kernel Functions and Profile#^def-kernel-function|kernel]] (a smooth bump) over every single data point in the dataset.​
2. Summing up all of these individual kernels to create a single, smooth curve.
3. The resulting curve represents the estimated probability density. Peaks in the curve indicate where data points are most concentrated.

#### From Kernels to KDE

![[kernel-functions-to-kde.png|#invert_B|700]]

###### Step 1: Place a Scaled Kernel at Each Data Point

For every single data point $X_{i}$ in my dataset $S=\{X_{1},X_{2},…,X_{n}\}$.
I place a kernel function directly on top of it i.e. each data point $X_{i}$ has a kernel function $\large K(\frac{X - X_{i}}{h})$.

*Each kernel function $K$, acts as a 'bump' or a small probability distribution, centered at its corresponding data point $X_{i}$ with its width (or "spread") controlled by the bandwidth $h$*.

Two crucial adjustments are made to the kernel at this stage:
1. **Scaling by Bandwidth ($h$):** The kernel is "stretched" or "squashed" by the bandwidth $h$. This controls the width of the bump, determining how far its influence spreads.
2. **Height Adjustment:** To ensure the area under this scaled kernel remains $1$ (a valid probability), I divide $K$ by $h$.​

So, for a single data point $X_i$, the contribution to the final density is a self-contained little probability distribution given by $\frac{1}{h} \cdot K(\frac{X - X_i}{h})$.

>[!tip] Bandwidth of Kernel Functions
>The core principle is that **each individual kernel placed on a data point must function as a complete, self-contained probability distribution**. By definition, any probability density function (PDF) must have a total area under its curve equal to $1$.

###### Step 2: Sum All the Kernel Functions

Now, I simply add all these individual kernel "bumps" together. At any given point $X_{i}$, its total density is the sum of the heights of all the kernel functions at that exact spot.
- In regions where data points are clustered closely together, many kernel bumps will overlap, and their heights will add up to create a high peak in the final curve.
- In regions where data points are sparse, the kernel bumps will be far apart, resulting in low valleys in the curve.

###### Step 3: Normalize by the Number of Data Points

The sum of individual kernel functions creates the *correct shape of the density curve*, but the total area under this combined curve is now equal to $n$ (the number of data points), because I added $n$ individual probability distributions, each with an area of 1, so to make the final curve a valid PDF, I divide the whole thing by $n$.

###### KDE function

> [!cite] Kernel Density Estimation Function
> The final Probability density function $\hat{f}_{h}(X)$, for a dataset $S=\{X_{1},X_{2},…,X_{n}\}$ where the kernel function is $K$, is described by:
>
> $$\hat{f}_h(X) = \frac{1}{n} \sum_{i=1}^{n} \frac{1}{h} K\left(\frac{X - X_{i}}{h}\right) = \frac{1}{nh} \sum_{i=1}^{n} K\left(\frac{X - X_{i}}{h}\right)$$

^def-kde-function

>[!danger] KDE function where the kernel function is implicity normalized.
>KDE function might sometimes be defined as:
>
>$$P(X) = \frac{1}{n} \sum_{i=1}^{n} K\left(\frac{X - X_{i}}{h}\right)$$
>
>Here the kernel function is implicitly normalized. In this form, the function $K$ is **not the standard kernel**. It represents a "scaled kernel" that has **already been normalized for its bandwidth**. The $\frac{1}{h}$ factor is considered to be part of the $K$ function itself.
>

#### Bandwidth of Kernel Functions and KDE

 The bandwidth ($h$) **acts as a smoothing parameter** for kernel functions in Kernel Density Estimation (KDE). It dictates the shape and smoothness of the final density curve more than the choice of kernel function itself. Think of bandwidth as controlling the "reach" or "influence" of each individual data point's kernel.​

*Imagine each data point is a light source (the kernel). The bandwidth $h$ is the dial that controls the width and intensity of the light beam. The final KDE curve is the total brightness you see along the number line from all the lights combined.*

For $x = \{3, 4, 7\}$, and $h \in \{ 0.2, 0.3, 0.4, 0.5, 1, 1.5 \}$, using the normal Kernel, the six different kernel density estimations are:
![[kernel-functions-low-vs-high-bandwith.svg|]]

###### Small Bandwidth: Under-smoothing and Overfitting

When I use a small bandwidth, I'm essentially giving each data point a very narrow and intense "spotlight."
- **Effect on Kernels:** Each kernel function becomes a tall, narrow spike centered directly over its data point. Its influence does not extend far.
- **Resulting Curve:** Because the kernels don't overlap much, the final density estimate will be a series of sharp peaks, with a distinct "bump" for almost every individual data point. The *curve will appear very spiky and jagged*.​
- **Overfitting:** This spiky curve is "overfitted" to the sample data. It's capturing the random noise and peculiarities of the specific data points I have, rather than representing the smoother, true underlying distribution of the population from which the sample was drawn. The model has *high variance* because small changes in the input data would lead to a drastically different-looking curve.​

###### Large Bandwidth: Over-smoothing and Underfitting

When I use a large bandwidth, I'm giving each data point a very wide, dim "floodlight."

- **Effect on Kernels:** Each kernel function becomes a very wide, short, and gentle bump. Its influence spreads far across the range of the data.
- **Resulting Curve:** The wide kernels overlap and blend together so much that they wash out any fine details. The resulting *curve is overly smooth* and may obscure important structural features of the data, such as the presence of multiple peaks (multi-modality). In extreme cases, it can look like a single, broad, featureless mound.​
- **Underfitting:** This overly smooth curve is "underfitted." The model is too simple and has failed to capture the true underlying structure of the data. It has *high bias* because the shape of the estimate is more reflective of the wide kernel shape than the actual distribution of the data points.

###### The Goal: Optimal Bandwidth and the Bias-Variance Trade-off

The choice of bandwidth is a classic example of the **bias-variance tradeoff** in statistics. The goal is to select a bandwidth that minimizes the total error of the estimate (often measured by the Mean Integrated Squared Error, or `MISE`), which is a function of both squared bias and variance.

#### Gradient of $P(X)$

I start by defining the Probability density function in terms of the kernel profile.

$$P(X) = \frac{1}{n}\sum_{i=1}^{n} K\left(\frac{X-X_i}{h}\right) = \frac{1}{n}\sum_{i=1}^{n} c \cdot k\left(\left\|\frac{X-X_i}{h}\right\|^2\right)
$$

Now applying the gradient,

$$\nabla_X P(X) = \nabla_{X}\left[\frac{1}{n}\sum_{i=1}^{n} c \cdot k\left(\left\|\frac{X-X_{i}}{h}\right\|^{2}\right)\right]$$

Using the [[Gradient#^def-sum-rule-gradient|sum rule]],

$$\nabla_X P(X) = \frac{1}{n}\sum_{i=1}^{n} c \cdot \nabla_X k\left(\left\|\frac{X-X_i}{h}\right\|^2\right)$$

Further simplifying (same as gradient of kernel functions)

$$\begin{align*} \nabla_{X} P(X) &= \frac{1}{n}\sum_{i=1}^{n} c \cdot k'\left(\left\|\frac{X-X_{i}}{h}\right\|^{2}\right) \cdot 2\left(\frac{X-X_i}{h^{2}}\right) \\ &= \frac{2c}{nh^{2}}\sum_{i=1}^{n} (X_{i} - X)\left\{-k'\left(\left\|\frac{X-X_{i}}{h}\right\|^{2}\right)\right\} \\ &= \frac{2c}{nh^{2}}\sum_{i=1}^{n} (X_{i} - X) \ g\left(\left\|\frac{X-X_{i}}{h}\right\|^{2}\right)\end{align*}$$

> [!cite] Gradient of KDE function
>
> The Gradient $\nabla_{X}P(X)$ of the Kernel Density Estimate $P(X)$ is given as:
>
> $$\nabla_X P(X) = \frac{2c}{nh^{2}}\sum_{i=1}^{n} (X_{i} - X) \ g\left(\left\|\frac{X-X_i}{h}\right\|^{2}\right)
> $$

^def-gradient-kde

## Histograms as Density Estimators

A histogram is a **non-parametric estimator** of the probability density function (PDF) of a continuous random variable.

>[!cite] Histogram Function
>Let $f(x)$ be the unknown Probability Density Function that I wish to estimate. I partition the support of the distribution into a set of disjoint intervals or "bins," $\{B_{1},B_{2},…,B_{m}\}$. Where the width of bin $B_j$ is $b_{wj}$.
>
>The histogram function, $\hat{f}_n(x)$ represents the **estimated probability density** at any given point $x$, and is defined as:
>
>$$\large \hat{f}_n(x) = \sum_{j=1}^{m} \left( \frac{1}{n \cdot b_{wj}} \sum_{i=1}^{n} \mathbb{I}_{B_j}(x_i) \right) \mathbb{I}_{B_j}(x)$$

##### Interpretation of the Histogram Function

The true PDF is a smooth curve that describes the likelihood of a random variable taking on different values. The *histogram function $\hat{f}_n(x)$ is a blocky, staircase-like approximation* of that smooth curve, constructed from the finite set of sample data.

###### Inner Summation

The empirical probability (relative frequency) of an observation falling into bin $B_j$, is given: $$P_n(B_{j})= \frac{\text{number of } x_i \text{ in } B_j}{n}$$

The inner part is basically the height of the bin $B_{j}$, that is:

$$\text{Height}_{B_j} = \frac{1}{n \cdot b_{wj}} \sum_{i=1}^{n} \mathbb{I}_{B_j}(x_{i}) = \frac{P_{n}(B_{j})}{b_{wj}}$$

$$
\hat{f}_n(x) = \sum_{j=1}^{m} (\text{Height}_j) \cdot \mathbb{I}_{B_j}(x)
$$

$$\hat{f}_n(x) = \frac{\text{number of } x_i \text{ in } B_j}{n \cdot b_{wj}} = \frac{P_n(B_j)}{b_{wj}} \quad \text{for } x \in B_j
$$

1. **Inner Summation:** $\large \sum_{i=1}^{n} \mathbb{I}_{B_j}(x_i)$
	This is the raw count of how many observations $x_i$ fall into the specific bin $B_{j}$. I'll call this $C_j$.
2. **Normalization Term:** $\large \frac{C_j}{n \cdot b_{wj}}$
	It divides the raw count $C_{j}$ by the total number of samples $n$ to get the relative frequency, and then divides by the specific width of that bin, $b_{wj}$. This ensures the *area of the bar is equal to the relative frequency*.
3. **Outer Summation and Indicator:** $\sum_{j=1}^{m} (\cdots) \mathbb{I}_{B_j}(x)$

	 This part makes function $\hat{f}_n(x)$ defined over the entire real line.