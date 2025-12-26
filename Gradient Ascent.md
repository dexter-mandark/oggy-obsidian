---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Mean Shift Clustering]]

---

## Gradient Ascent

Gradient ascent is an iterative optimization algorithm used to find the **local maximum of a function**. The basic idea is quite simple:
1. Start at some point on the function.
2. Calculate the gradient at that point. The gradient is a vector that points in the direction of the steepest uphill slope.​
3. Take a small step in the direction of that gradient.
4. Repeat from your new position.

>[!cite] Gradient Ascent
>This iterative process is described by the formula:
>
>$$X^{(t+1)} = X^{(t)} + \eta_t \nabla_X P(X^{(t)})$$
>
> Where,
> - $X^{(t)}$: is the **current position** at iteration $t$
> - $X^{(t+1)}$: is the **new position** after taking one step.
> - $\nabla_{X}P(X^{(t)})$: is the **gradient** of the density function $P(X)$ evaluated at the current position. This vector tells the direction of the steepest ascent.
> - $\eta_{t}$: is the **step size** (or learning rate) at iteration $t$. It's a small positive number that controls how far I move in the gradient's direction.
^def-gradient-ascent

*The algorithm basically says: "My next position is the current position, plus a small step in the direction of the steepest uphill slope."*

###### The Gradient Points Uphill

At any point $X^{(t)}$ on the density surface, the gradient $\nabla_{X}P(X^{(t)})$ is a vector that points in the direction where the function $P(X)$ increases most rapidly. By taking a step in this direction, I'm guaranteed to move to a location with higher density (assuming the step size $\eta_{t}$ is small enough).

###### Iterative Climbing

Each iteration moves me from a lower density region to a higher density region. As I repeat this process:
- When I'm far from a peak, the gradient is large, so I take big steps
- As I approach a peak, the gradient becomes smaller, so my steps naturally shrink
- Eventually, I reach a point where the gradient is approximately zero

###### Convergence at the Peak

When $\nabla_{X}P(X^{(t)}) \approx 0$, the update formula gives $X^{(t+1)} = X^{(t)}$ meaning I stop moving. This happens precisely at a local maximum—the peak of the hill you've been climbing.

The algorithm converges because:
1. Each step increases the function value: $P(X^{(t+1)}) \geq P(X^{(t)})$
2. The function is bounded above (densities can't be infinite)
3. By the **monotone convergence theorem**, the sequence must converge to a stationary point
