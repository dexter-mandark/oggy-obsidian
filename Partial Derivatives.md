---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Derivatives

> [!cite] Difference quotient
> The **difference quotient** calculates the **average rate of change** of a function over a small interval. It is defined as:
>
> $$\frac{\delta x}{\delta y} = \frac{f(x + \delta x) - f(x)}{\delta x}$$
>
> Geometrically, it computes the slope of the **secant line** that passes through two distinct points on the graph of the function $f(x)$.


> [!cite] Derivative
> The **derivative** of a function at a specific point measures the **instantaneous rate of change** at that exact point. It is formally defined as the limit of the difference quotient as the interval approaches zero.
>
> $$\frac{df}{dx} = \lim_{h \to 0} \frac{f(x + h) - f(x)}{h}$$
>
> Geometrically, the derivative gives the slope of the **tangent line** to the function's graph at a single point.


![[tangent-secant-line.svg|300]]

You start with the difference quotient, which gives the slope of a line between two points. As you bring these two points infinitesimally close together (by making the interval $h$ or $\delta x$ approach zero), the secant line becomes a tangent line, and the average rate of change becomes the instantaneous rate of change. In essence, *the derivative is what the difference quotient "becomes" at a single point.*

#### Differentiation Rules

###### Constant Rule

The derivative of any constant is always zero. If $c$ is a constant, then

###### Power Rule

$$\frac{d}{dx}(x^n) = nx^{n-1}$$

​

###### Constant Multiple Rule

When a constant is multiplied by a function, the constant remains unchanged during differentiation:

$$\frac{d}{dx}(cf(x)) = c\frac{d}{dx}(f(x))$$

​

###### Sum and Difference Rule

The derivative of a sum or difference of functions equals the sum or difference of their individual derivatives:​

$$\frac{d}{dx}(f(x) \pm g(x)) = \frac{d}{dx}(f(x)) \pm \frac{d}{dx}(g(x))$$

For example, ddx(x3+x2)=3x2+2x\frac{d}{dx}(x^3 + x^2) = 3x^2 + 2xdxd(x3+x2)=3x2+2x.[cuemath](https://www.cuemath.com/calculus/derivative-rules/)​

###### Product Rule

When two functions are multiplied together, the product rule states:

$$\frac{d}{dx}(f(x) \cdot g(x)) = f(x) \cdot g'(x) + f'(x) \cdot g(x)$$

###### Quotient Rule

For the division of two functions:

$$\frac{d}{dx}\left(\frac{f(x)}{g(x)}\right) = \frac{g(x) \cdot f'(x) - f(x) \cdot g'(x)}{[g(x)]^2}$$

###### Chain Rule

This rule is used for differentiating composite functions. If $y = g(u)$ and $u = f(x)$, then​

$$\begin{align} \frac{dy}{dx} &= \frac{dy}{du} \cdot \frac{du}{dx} \\ \\ \frac{d\Big[g(f (x))\Big]}{dx} &= \frac{d\Big[g(f(x))\Big]}{d[f(x)]}\cdot\frac{df(x)}{dx} =g'(f (x))\cdot f'(x) \end{align}$$
