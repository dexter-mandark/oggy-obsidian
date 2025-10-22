---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Kernel Functions

A kernel is a **symmetric, peaked function** that assigns weights to data points based on their distance from a specific point.

> [!cite] Kernel Functions
> A kernel is a function, $K(u)$, that satisfies the following conditions:
> - **Non-negative:** The function's value must be zero or positive for all inputs.
> - **Symmetric:** The function must be symmetric around zero, meaning it's an "even function" where $K(u) = K(-u)$ . This ensures that the weighting is the same for points at an equal distance on either side of the center.
> - **Normalized:** I should be able to treat the kernel itself as a probability density function. The total area under the function's curve must equal one i.e. $\int_{-1}^{1}K(u).du = 1$.
^def-kernel-function

Because a kernel is symmetric and its values typically decrease as the input $u$ moves away from zero, the function naturally forms a peak at its center ($u=0$).
This shape is fundamental to its role as a weighting function:
- The **peak at the center** gives the maximum weight to the point being evaluated.
- The **slopes on either side** assign progressively smaller weights to other data points based on their distance from the center.

#### Histograms as Density Estimators

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