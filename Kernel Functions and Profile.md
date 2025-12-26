---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Kernel Density Estimation]]

---

## Kernel Functions

A kernel is a **symmetric, peaked function** that assigns weights to data points based on their distance from a specific point.


![[common_kerenl_functions.png|#invert_B|700]]



> [!cite] Kernel Functions
> A kernel is a function, $\large K(\frac{X - \mu}{h})$, where,
> - $\mu$ is the centroid where the function peaks.
> - $h$ is the bandwidth
> - $X$ and $\mu$ are $N$-dimensional vectors.
> 
> The kernel function satisfies the following conditions:
> - **Non-negative:** The function's value must be zero or positive for all inputs.
> - **Symmetric:** The function must be symmetric around zero, meaning it's an "even function" where $K(X) = K(-X)$ . This ensures that the weighting is the same for points at an equal distance on either side of the center.
>
> - **Normalized:** I should be able to treat the kernel itself as a probability density function. The total area under the function's curve must equal one, $$\int_{\mathbb{R^{N}}}K\big(\frac{X - \mu}{h}\big).dX = 1$$
>
> ![[kernel_functions.png|#invert_B|400]]

^def-kernel-function


- The **peak at the center** gives the maximum weight to the point being evaluated. The kernel function, peaks at $X=\mu$. And its values typically decrease as the input $X$ moves away from the center.
- The bandwidth $h$ decides the **rate of decay**, it controls how quickly the kernel function decays as input value $X$ moves away from the center.

>[!tip] Effects of bandwidth on the kernel function
>The choice of bandwidth has the effect of horizontally stretching or squashing the kernel's shape:
> - If $h > 1$, the kernel becomes **wider**.
> - If $h < 1$, the kernel becomes **narrower**.
> 
> The band-width controls the 'width' of the kernel function and as the area under curve must remain $1$, an increase in width ($h>1$) must be compensated by a decrease in height, making the function wider and shorter. And for ($h<1$), height must be increased, making the function narrower and taller.
>
> ![[standard-gaussian-kernel-and-effects-of-bandwith.png#invert_B|700]]

#### Gaussian Kernel

> [!cite] Gaussian Kernel
> The Gaussian Kernel function $K_{N}(\frac{X - \mu}{h})$, is defined as:
>
> $$
> K_N\!\left(\frac{X - \mu}{h}\right) = c_{N}\cdot \exp\!\left(-\frac{1}{2} \left\lVert \frac{X - \mu}{h} \right\rVert^2 \right)
> $$
>
> Where $c_{N}$ is the normalization factor (constant) that ensures the total area under the curve equals one.
>
> For the standard normal distribution, $$c_{N} = \frac{1}{2\pi}$$
> ![[Gaussian_kernel_function.png|#invert_B|500]]
^def-gaussian-kernel

The function calculates a weight based on the exponential of the negative squared [[Norm#^def-euclidean-distance|Euclidean distance]] between a point $X$ and the center $\mu$.​
- **Weighting Behavior:** The weight is highest ($c_{N}$) when $X = \mu$. As $X$ moves away from $\mu$, the squared distance $\| X -\mu\|^{2}$ increases, causing the negative exponent to become larger, and the *weight to decay exponentially towards zero*.
- **Infinite Support:** A defining characteristic of the Gaussian kernel is that *its value never technically reaches zero*; it just becomes infinitesimally small. This means *every data point in the dataset has some (although negligible) influence* on the density estimate at every other point.

#### Epanechnikov Kernel Function

> [!cite] Epanechnikov Kernel Function
> The Epanechnikov Kernel Function, $K_{E}(\frac{X - \mu}{h})$, uses a simple inverted parabola to assign weights and is defined as:
>
> $$
> K_E\!\left(\frac{X - \mu}{h}\right) =
> \begin{cases}
> c_E \left( 1 - \left\lVert \frac{X - \mu}{h} \right\rVert^2 \right), & \text{if } \lVert X - \mu \rVert \leq h \\
> 0, & \text{if } \lVert X - \mu \rVert > h
> \end{cases}
> $$
>
> Where $c_{E}$ is the normalization constant that ensures the integral is one.
> For the standard one-dimensional Epanechnikov kernel, $c_{E} = \frac{3}{4}$
>
> ![[Epanechnikov_kernel_function.png|#invert_B|500]]

^def-epanechnikov-kernel-function

- **Weighting Behavior:** The weight is at its maximum ($c_{E}$) at $X = \mu$. It *decreases in a parabolic fashion* until it hits exactly zero when the $\| X - \mu \| = h$.
- **Compact Support:** Unlike the Gaussian kernel, the Epanechnikov kernel has compact support. This means its *influence is strictly limited to a finite range*. Any data point $X$ outside the distance $h$ from the center $\mu$ receives a weight of exactly zero. This makes it *computationally more efficient than the Gaussian kernel* for large datasets​.

###### Interpretation

The standard parabola with vertex at $(h,k)$ is: $y=a(x−h)^{2}+k$.
If I expand and compare, I can rewrite the Epanechnikov kernel as: $K_E(x) = c_E - \frac{c_E}{h^2}(x - \mu)^2$

This is of the same form: $y = -\frac{c_E}{h^2}(x - \mu)^2 + c_E$

So it’s literally a **parabola**:
- Opens downward because $a < 0$.
- Has vertex at $(\mu, c_{E})$
- Has width controlled by $h$ (larger $h\rightarrow$ wider parabola).
- Has height controlled by $c_E$​
- For the 1-D case, because $\int_{−1}^{1​}K_{E}​(u)du=1$, this gives $c_{E} = \frac{3}{4}$​.

## Kernel Profile Functions

> [!cite] Kernel Profile Function
> Kernel Profile Function, denoted as $k(X)$, is the fundamental component that defines the _shape_ of a kernel.
>
> $$K\!\left( \frac{X - \mu}{h} \right) = c \cdot k\!\left( \left\lVert \frac{X - \mu}{h} \right\rVert^{2} \right)
> $$

^def-kernel-profile-function

I break down the kernel function $K(X)$ into two parts:
- **Normalization Constant ($c$)**: A number that scales the function to ensure the total area under the curve equals $1$.
- **Kernel Profile Function ($k$)**: A simpler function that dictates the kernel's cross-sectional shape.

#### Kernel Profile Functions as an Abstraction

Kernel profile function is a mathematical **abstraction** of kernel function. And as any abstraction does, **it separates concerns**. It directly gives me *weighting factor* (that determines how much a data point at that distance contributes to the density estimate) *without being concerned about the dimensionality of the data.*

Although the kernel function $K(X)$ is defined for multi-variate data ($X$ is an $d$-dimensional vector), the internal mechanics of the function $K$ depend on the number of dimensions ($d$) of the $X$ vectors.

I take the Gaussian kernel function $K(u)$, where $u = \frac{X - X_{i}}{h}$
- For $1$-D data: $u$ is a scalar. The Gaussian kernel is $K(u) = c \cdot exp(\frac{-u^{2}}{2} )$
- For $2$-D data: $u$ is a vector $(u_{1}, u_{2})$. And $K(u) = c \cdot exp\left(\frac{-(u_{1}^{2} + u_{2}^{2})}{2} \right)$
- For 3-D data: $(u_{1}, u_{2}, u_{3})$. And $K(u) = c \cdot exp\left(\frac{-(u_{1}^{2} + u_{2}^{2} + u_{3}^{2})}{2} \right)$
- In general $K(\mathbf{u}) = c \cdot \exp \left( -\frac{1}{2} \sum_{i=1}^{N} u_i^2 \right)$

While the function gets more complex with each dimension, the underlying principle is the same: *the weight depends on the squared Euclidean distance from the mean.*

And that's exactly what the kernel profile function does. Instead of taking the data-point $X$ as input, the kernel profile function:
- Takes $\left\lVert \frac{X - \mu}{h} \right\rVert^{2}$ as input
- And the output is a *non-negative scalar* value representing a **weight** or **relative influence**.
	- High output value (close to $1$): The point is very close, so it has strong influence on the local density.
	- Low output value (close to 0): The point is far away, so it has weak or negligible influence.
- Since distance is always a single non-negative number, its is a much **simpler, one-dimensional function**.

>[!tip] Kernel vs Kernel Profile
>A kernel is a function that measures **influence** or **similarity** between points — usually based on their distance.
>
>The **kernel profile** (or **kernel shape**) describes **how that influence decreases** (or _decays_) as the distance between two points increases.
>The kernel profile essentially encodes the shape of the decay function — how quickly the influence of a point falls off with distance

## Gradient of Kernel Function

>[!cite] Shadow Kernel
>The shadow kernel function $g(x)$, is the negative derivative of the kernel profile function.
>
>$$g(x) = -\frac{d}{dx}\{k(x)\}$$

^def-shadow-kernel


>[!cite] Gradient of the Kernel Function
> I'm trying to find the **gradient $\nabla_X K$ of the kernel function**, $K$ with respect to $X$.
>
> $$\begin{align*} \nabla_X K\left(\frac{X-\mu}{h}\right) &= c\nabla_X k\left(\left\|\frac{X-\mu}{h}\right\|^{2}\right) \\&= c \ k'\left(\left\|\frac{X-\mu}{h}\right\|^{2}\right) 2\left(\frac{X-\mu}{h^{2}}\right) \\ &= \frac{2c}{h^{2}}(\mu-X)\left\{-k'\left(\left\|\frac{X-\mu}{h}\right\|^{2}\right)\right\} \end{align*}$$
>
> Substituting $g(x) = -k'(x)$, the gradient of the kernel function is defined as:
>
> $$\nabla_X K\left(\frac{X-\mu}{h}\right) = \frac{2c}{h^{2}}(\mu-X) \ g\left(\left\|\frac{X-\mu}{h}\right\|^2\right)$$

^def-gradient-of-kernel-function

>[!example] Gradient of Euclidean Distance
>Prove that the gradient of $\left\|\frac{X - \mu}{h}\right\|^2$ with respect to $X$ is equal to $\frac{2(X - \mu)}{h^2}$
>
> Let $f(X) = \left\|\frac{X - \mu}{h}\right\|^2$, then writing out $f(X)$ explicitly:
>
> $$f(X) = \left\|\frac{X - \mu}{h}\right\|^{2} = \left(\frac{x_1 - \mu_1}{h}\right)^{2} + \left(\frac{x_2 - \mu_2}{h}\right)^{2} \cdots \left(\frac{x_n - \mu_n}{h}\right)^{2}$$
>
> And $\nabla_X f(X) = \left[\frac{\partial f}{\partial x_{1}}, \frac{\partial f}{\partial x_{2}}, \cdots ,\frac{\partial f}{\partial x_{n}} \right]$
>
> For $\frac{\partial f}{\partial x_{1}}$, other than $x_{1}$ the derivative of all the terms will be zero with respect to $x_{1}$.
>
> $$
> \begin{align} \frac{\partial f}{\partial x_{1}} &= \frac{\partial}{\partial x_{1}}\left\{\left(\frac{x_{1} - \mu_{1}}{h}\right)^{2} + \left(\frac{x_{2} - \mu_{2}}{h}\right)^{2}+ \cdots + \left(\frac{x_{n} - \mu_{n}}{h}\right)^{2}\right\} \\ &= \frac{\partial}{\partial x_1}\left(\frac{x_1 - \mu_1}{h}\right)^{2} \\ &= 2 \cdot \left(\frac{x_1 - \mu_1}{h}\right) \cdot \frac{1}{h} \\ &= \frac{2(x_1 - \mu_1)}{h^2}\end{align}
> $$
>
> Similarly, $$\begin{align} \frac{\partial f}{\partial x_{2}}  &= \frac{2(x_{2} - \mu_{2})}{h^{2}} \\ \\ \frac{\partial f}{\partial x_n}  &= \frac{2(x_n - \mu_n)}{h^2}\end{align}$$
>
> And,
>
> $$\begin{align} \nabla_X f(X) &= \left[\frac{2(x_{1} - \mu_{1})}{h^{2}}, \frac{2(x_{2} - \mu_{2})}{h^{2}}, \cdots , \frac{2(x_{n} - \mu_{n})}{h^{2}} \right]\\ &= \frac{2(X - \mu)}{h^{2}} \end{align} $$

###### Gaussian Kernel Profile Function

For the Gaussian Kernel let $U = \frac{X - \mu}{h}$ then,
$$K_N\!\left(U\right) = c_{N}\cdot \exp\!\left(-\frac{1}{2} \left\lVert U \right\rVert^2 \right)$$
As its already in the form of a normalizing constant multiplied by a function of $\|U\|^2$, Let  $x = \|U\|^2$ then,

$$K_N\!\left(U\right) = c_{N}\cdot \exp\!\left(-\frac{1}{2} \left\lVert U \right\rVert^2 \right) = c_{N}\exp\left(-\frac{x}{2}\right)= c_N.k_N(x)$$
The Gaussian kernel profile function is simply, $$\boxed{k_N(x) = \exp(-\frac{x}{2})}$$
And the shadow kernel $g_N(x)$ that is, the negative derivative of $k_N(x)$ is simply
$$\boxed{g_N(x) = \frac{1}{2}\exp(-\frac{x}{2})}$$

###### Epanechnikov Kernel Profile Function

Similarly, for the Epanechnikov kernel let $U = \frac{X - \mu}{h}$ then,
$$K_E\!\left(U\right) =
\begin{cases}
c_E \left( 1 - \left\lVert U \right\rVert^2 \right), & \text{if } \lVert U \rVert \leq 1 \\
0, & \text{if } \lVert U \rVert > 1
\end{cases}$$

The Epanechnikov kernel profile function is simply, 
$$\boxed{k_E\!\left(x\right) =
\begin{cases}
1 - x, & \text{if } x \leq 1 \\
0, & x > 1
\end{cases}}$$
And the shadow kernel is, 
$$\boxed{g_E\!\left(x\right) =
\begin{cases}
1, & \text{if } x \leq 1 \\
0, & x > 1
\end{cases}}$$


