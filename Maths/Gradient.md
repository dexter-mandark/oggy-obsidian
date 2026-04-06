---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Kernel Functions and Profile]], [[Gradient Ascent]]

---

>[!cite] Gradient
>The gradient of a function $f(X)$ with respect to $X$, written as $\nabla_{X}f(X)$ is simply a vector containing the partial derivatives with respect to each of $X$'s components.
>
>$$\nabla_X f(X) = \left[\frac{\partial f}{\partial x_{1}}, \frac{\partial f}{\partial x_{2}}, \cdots ,\frac{\partial f}{\partial x_{n}} \right]$$
>
>In simple terms, the gradient tells me: "If I move $X$ slightly, in which direction does $f(X)$ increase the most?"


>[!tip] Sum rule for gradients
>Because the gradient is a **linear operator**, it has a property called the "sum rule": the gradient of a sum equals the sum of the gradients.
>
>$$\nabla_{X}\left(\sum_{i=1}^{n}f_{i}(X)\right) = \sum_{i=1}^{n}\nabla_{X}f_{i}(X)$$
^def-sum-rule-gradient

>[!cite] First order taylor Optimization
> In single-variable calculus, if I have a function $y = f(x)$ and I want to know its value at a point *near* $x = a$, I use the **Taylor approximation**:
>
> $$
> f(a + \Delta x) = y + \Delta y \approx f(a) + \Delta x \cdot f'(a)
> $$
> This works because $f'(a)$ tells me the *rate of change* of $f$ at $a$. If I move a tiny distance $\Delta x$ away from $a$, the function's output changes by approximately:  $\Delta y \approx \Delta x \cdot f'(a)$. The smaller $\Delta x$ is, the more accurate this approximation becomes.
>
> Now my function takes a vector input: $f(X)$ where $X \in \mathbb{R}^n$. I'm at point $X$, and I want to move to a nearby point. In multiple dimensions, a "nearby point" is written as $X + \Delta X$, where $\Delta X$ is a small displacement vector.
>
> The **first-order Taylor approximation** generalizes to:
> $$
> f(X + \Delta X) \approx f(X) + \Delta X^T \nabla f(X)
> $$
>
> The gradient $\nabla f(X)$ plays the exact same role as $f'(a)$ in 1-D. It encodes the rate of change of $f$ in every direction simultaneously. The dot product $\Delta X^T \nabla f(X)$ picks out *how much $f$ changes* specifically in the direction of my step $\Delta X$.
^def-1st-taylor-approx