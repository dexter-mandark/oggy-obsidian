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
