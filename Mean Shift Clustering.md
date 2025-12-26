---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

#### Finding out the Modes

>[!cite] Modes in Gradient Ascent
>For the Kernel Density Estimate $P(X)$, a **mode** is a **local maximum**—a peak where the density is higher than all nearby points. Mathematically, at a local maximum $(X^{*})$ two conditions hold:
>- The **gradient is zero**: $\nabla_{X}P(X^{*}) = 0$ (I'm at a flat spot—no uphill direction exists)
>- The **curvature is negative**: The second derivative (Hessian matrix) is negative definite (it's a peak, not a valley)

###### Ascending the Hill

Substituting the [[Kernel Density Estimation#^def-gradient-kde|gradient of Kernel density estimate]], in the gradient ascent equation:

$$\begin{align*}
X^{(t+1)} &= X^{(t)} + \eta_t \nabla_X P(X^{(t)}) \\ \\
X^{(t+1)} - X^{(t)} &= \eta_t \left\{\frac{2c}{nh^2} \sum_{i=1}^{n} (X_i - X^{(t)})g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right)\right\} \\ \\
&= \frac{2c\eta_t}{nh^2} \left\{\sum_{i=1}^{n} X_i g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right) - \sum_{i=1}^{n} X^{(t)} g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right)\right\}
\end{align*}
$$

Let, $w_i = g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right)$, then I re-write this as:

$$X^{(t+1)} - X^{(t)} = \frac{2c\eta_t}{nh^2} \left\{ \sum_{i=1}^{n} X_i w_i - \sum_{i=1}^{n} X^{(t)} w_i \right\}
$$

In the second summation is a constant, I can take that out, i.e. $\sum_{i=1}^{n} X^{(t)} w_i = X^{(t)} \sum_{i=1}^{n} w_i$

$$
X^{(t+1)} - X^{(t)} = \frac{2c\eta_t}{nh^2} \left\{ \sum_{i=1}^{n} X_i w_i - X^{(t)} \sum_{i=1}^{n} w_i \right\}
$$

Now, I can factor out $\sum_{i=1}^{n}w_{i}$ from the whole equation,

$$X^{(t+1)} - X^{(t)} = \frac{2c\eta_t}{nh^2} \left(\sum_{i=1}^{n} w_i\right) \left\{ \frac{\sum_{i=1}^{n} X_i w_i}{\sum_{i=1}^{n} w_i} - X^{(t)} \right\}
$$

This, final equation can be interpreted as:

$$
X^{(t+1)} - X^{(t)} = \underbrace{\frac{2c\eta_t}{nh^2} \left(\sum_{i=1}^{n} w_i\right)}_{\text{Scalar coefficient}} \times \underbrace{\left\{ \frac{\sum_{i=1}^{n} X_i w_i}{\sum_{i=1}^{n} w_i} - X^{(t)} \right\}}_{\text{Mean Shift Vector}}
$$

The equation now has two parts:
- **Scalar part**: $\frac{2c\eta_t}{nh^2} \left(\sum_{i=1}^{n} w_i\right)$

- **Vector part**: $\frac{\sum_{i=1}^{n} X_i w_i}{\sum_{i=1}^{n} w_i} - X^{(t)}$

The direction is clear - it points from my current position to the weighted center of mass. But the *magnitude depends on the learning rate $n_{t}$, which is arbitrary*. How do I choose it?

###### Adaptive learning rate - Fuck the scalar part

Instead of picking a fixed $\eta_t$ I choose a value of $\eta_{t}$ that make the entire scalar coefficient equal to $1$.
Equating the whole scalar part to $1$ and, finding $\eta_t$

$$
\begin{align} & \frac{2c\eta_t}{nh^{2} \left(\sum_{i=1}^{n} w_{i}\right)} = 1 \\ \\
\eta_t &= \frac{nh^2}{2c} \cdot \frac{1}{\sum_{i=1}^{n}w_{i}} \end{align}
$$

Essentially, the learning the rate $\eta_{t}$ is now **adaptive**.
- $n, h, c$ are constants (fixed throughout the algorithm)
- $\sum_{i=1}^{n}w_{i}$ is the only thing that changes - it depends on where I am at iteration $t$.

The sum $\sum_{i=1}^{n}w_{i}$ represents the **total weight of all nearby points**. This is related to the local density:
- **High density** (many nearby points): $\sum_{i=1}^{n}w_{i}$ is *large* $\rightarrow \eta_{t}$ is *small*
- **Low density** (few nearby points): $\sum_{i=1}^{n}w_{i}$ is *small*  $\rightarrow \eta_{t}$ is *large*

*The value of $\eta_{t}$ is inversely proportional to the local density.*

The adaptive choice automatically adjusts:
- Far from peaks (low density) $\rightarrow$ large $\eta_{t} \rightarrow$ big jumps toward the weighted mean
- Near peaks (high density) $\rightarrow$ small $\eta_{t} \rightarrow$ smaller, careful steps

Two benefits of this:
- A fixed $n_{t}$, if its too small, it takes me a lot more iteration. If its too large, I might skip the local maxima entirely. Having the step size **inversely proportional to local density**, gives me the perfect convergence behavior.
- By setting the scalar to 1, I **completely eliminate the need to choose a learning rate**. The algorithm becomes parameter-free (except for bandwidth $h$)

###### Mean shift Update Rule - Just the vector part

Substituting the adaptive value of $\eta_{t}$, makes the scalar part as $1$, and what I'm left with is:

$$\begin{align} X^{(t+1)} - X^{(t)} &= \frac{\sum_{i=1}^{n} X_{i} w_{i}}{\sum_{i=1}^{n} w_{i}} - X^{(t)} \\ \\ X^{(t+1)} &=  \frac{\sum_{i=1}^{n} X_{i} w_{i}}{\sum_{i=1}^{n} w_{i}} \end{align}
$$

This is the pure **Mean Shift update rule**: the next position is simply the *weighted average* of all data points.

$$
X^{(t+1)} = \frac{\sum_{i=1}^{n} X_i g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right)}{\sum_{i=1}^{n} g\left(\left\|\frac{X^{(t)} - X_i}{h}\right\|^2\right)}
$$

## Mean shift Clustering

Mean shift clustering is a **non-parametric, density-based clustering algorithm** used to discover clusters, or "blobs," in a dataset *without requiring the number of clusters to be specified beforehand*. It operates by iteratively shifting data points toward regions of higher data density until they converge at local maxima, which represent the centers of the clusters.

The core idea behind mean shift is to treat data points as an empirical probability density function. For each data point, the algorithm defines a "kernel" or window of a certain radius and calculates the mean of all points within that window. The data point is then shifted to this newly calculated mean. This process is repeated, with each point moving towards a denser area, until the shifts become infinitesimally small, indicating convergence. Points that converge to the same location are considered part of the same cluster. Because of this process, it is also known as a **mode-seeking algorithm**.​​


Mean shift clustering can be understood through the following steps:

1. **Initialization** Each data point starts as a candidate for a cluster center​
    
2. **Density Estimation** For each data point, a window (or kernel) is defined around it, and the mean of all data points within this window is computed. This calculation is represented by the mean shift vector, which points toward the region of highest density.[](https://www.geeksforgeeks.org/machine-learning/ml-mean-shift-clustering/)​
    
3. **Shifting** Each data point is moved to the position of its calculated mean.​
    
4. **Convergence** Steps 2 and 3 are repeated until the positions of the data points stabilize, meaning the movement of each point falls below a certain threshold. These stable locations are the local maxima of the data's density function and become the cluster centers.​[](https://www.geeksforgeeks.org/machine-learning/ml-mean-shift-clustering/)​
    
5. **Cluster Assignment** Data points are grouped into clusters based on the final center to which they converged

