---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

In ordinary calculus, minimizing $f(X)$ means I'm free to move anywhere in $\mathbb{R}^n$, I just follow the gradient downhill until I hit a flat point, and I'm done. Constrained optimization takes that freedom away. I have a set of rules: *I must stay on this curve*, or *this quantity must not exceed this limit* and now I have to find the best value of $X$ that minimizes $f(X)$ while obeying those rules. The rules are my **constraints**, and the function I'm optimizing is the **objective function**.

The goal is not to search the entire input space freely, I'm **confined to a lower-dimensional surface** defined by the constraint, and I want to find the highest (or lowest) point on that surface.

###### Two Kinds of Constraints

Constraints come in exactly two flavors:

- **Equality constraints** $h_j(X) = 0$: I'm *glued* to a surface. Every point in the solution must lie exactly on it. No wiggle room. Example: solution must lie on the unit circle $x_1^2 + x_2^2 = 1$.
- **Inequality constraints** $g_i(X) \leq 0$: I'm confined to a *region*. I can be anywhere inside it or on its boundary. Example: $2x_1 + x_2 - 2 \leq 0$.

The collection of all points that satisfy every constraint simultaneously is called the **feasible set** $\mathcal{F}$. And my entire job is to find the point in $\mathcal{F}$ where $f(X)$ is smallest.

###### Formal Definition

>[!cite] Constraint optimisation Problem
>A constrained optimization problem is defined as finding a vector $X^* \in \mathbb{R}^n$ that solves: $$\begin{align}  & \min_{X \in \mathbb{R}^n}  f(X)  \\ \text{subject to} & \quad g_i(X) \leq 0, \quad i = 1, \ldots, m \quad \\ &  \quad  h_j(X) = 0, \quad j = 1, \ldots, l   \end{align}$$
>
>where $f : \mathbb{R}^n \to \mathbb{R}$ is the **objective function**, $g_i : \mathbb{R}^n \to \mathbb{R}$ are **inequality constraint functions**, and $h_j : \mathbb{R}^n \to \mathbb{R}$ are **equality constraint functions**.
>
>The feasible set (or feasible region) is the set of all points that simultaneously satisfy every constraint:$$\mathcal{F} = \{ X \in \mathbb{R}^n \mid g_i(X) \leq 0 \; \forall i, \; h_j(X) = 0 \; \forall j \}$$
>
>The problem then reduces to: find $X^* \in \mathcal{F}$ such that $f(X^*) \leq f(X)$ for all $X \in \mathcal{F}$.
>A solution $X^*$ is a **global minimum** if this holds over all of $\mathcal{F}$.
>And a **local minimum** if it holds within some open ball $\mathcal{B}(X^*, \epsilon) \cap \mathcal{F}$ for some $\epsilon > 0$.
^def-constraint-optimization

###### Active and Inactive Constraints

A constraint $g_i(X) \leq 0$ is called **active** at a point $X^*$ if $g_i(X^*) = 0$, and **inactive** if $g_i(X^*) < 0$.
Active constraints are geometrically significant, they form the boundary of $\mathcal{F}$ where the optimal solution lives. Equality constraints $h_j(X) = 0$ are, by definition, always active.

## Equality Constraint

###### Contour Lines and the Gradient

A **contour plot** of $f(x_1, x_2)$ is a top-down map where each line connects all points with the same value of $f$.

The **gradient** $\nabla f$ is a vector that always points perpendicular to the contour lines, in the direction of steepest ascent. To decrease $f$, I move against the gradient.

This is the engine of unconstrained optimization. The problem is: in constrained optimization, moving against $\nabla f$ may take me outside $\mathcal{F}$. I need a rule for moving that respects the boundary

>[!abstract]- Example Plot
>Plot for the function $f(x, y) = xy +1$
>![[z=xy+1_2.png|600]]
>![[z=xy+1_1.png|600]]
>![[z=xy+1_contour.png|600]]

Let my constraint be a simple equality condition $h(X) = 0$, for $X \in \mathbb{R}^n$ this defines a *surface*. **My solution $X^*$ must lie exactly on this surface. I cannot leave it**.

If I stand at point $X$ and want to increase $f$ as fast as possible, I walk in the direction of the gradient $\nabla f(X)$, the gradient is always perpendicular to the contour lines. And a contour line is a curve along which $f$ stays constant. *The direction of steepest change (gradient) is always perpendicular to the direction of no change (contour lines).*

Similarly, $\nabla h(X)$ points *perpendicular to the constraint surface*. It points in the direction that would take me off the constraint surface fastest.

###### Allowed Moves: Tangency condition

At any point $X$ on the surface $h(X) = 0$, I can ask: *which directions can I move in without immediately leaving the constraint surface?*

If I move from $X$ in direction $v$ by a tiny step $\epsilon$, I land at $X + \epsilon v$. And I need this new point to still (approximately) satisfy the constraint $h(X + \epsilon v) = 0$.
Using the [[Gradient#^def-1st-taylor-approx|first-order Taylor approximation]]:
$$
\begin{align} h(X + \epsilon v) \approx h(X) + (\epsilon \, v)^T \nabla h(X) \\  \approx h(X) + \epsilon \, \nabla h(X)^T v  \end{align}
$$

Since $X$ is already on the surface, $h(X) = 0$. And since $\epsilon \neq 0$ (I actually took a step), to still satisfy the constraint $h(X + \epsilon v) = 0$,  I need $\nabla h(X)^T v = 0$. That's the **tangency condition**.  All such directions $v$ form the **tangent space** of the constraint at $X$.
Formally, a direction $d$ is tangent to the constraint surface at $X$ if it satisfies $\nabla h(X)^T d = 0$.

Since I must stay on the constraint surface, *the only moves I'm allowed to make are **tangential** to the constraint surface*. Any allowed movement direction $d$ must satisfy $\nabla h(X)^T d = 0$. In other words, *my movement direction must be perpendicular to $\nabla h$.*

I move tangentially along the constraint surface and increase $f$. The rate of change of $f$ in direction $d$ is: $\nabla f(X)^T d$

###### Optimality Condition

*Now if I stand at a candidate optimal point $X^*$ on the constraint surface and claim $f(X^*)$ is the minimum. What must be true?*

For every tangent direction $d$ (every allowed move), the function $f$ must not decrease. Otherwise you could take a small step in direction $d$ and find a lower value of $f$, contradicting optimality. Mathematically, for all $d$ with $\nabla h(X^*)^T d = 0$:

$$
\nabla f(X^*)^T d \geq 0
$$
But since $d$ is a direction along a smooth surface, the condition must hold for both $d$ and $-d$:
$$
\nabla f(X^*)^T v \geq 0
\quad \text{and} \quad
\nabla f(X^*)^T (-v) \geq 0
$$
The only this way both these equations can be satisfied simultaneously is when:
$$
\nabla f(X^*)^T d = 0
\quad \text{for all tangent directions } d
$$

The gradient of $f$ has **zero projection onto every tangent direction** and it is entirely **orthogonal to the constraint surface.**

###### Gradients must align

I have two vectors, both perpendicular to the same tangent space:
- $\nabla f(X^*)$ is perpendicular to the constraint surface because of optimality
- $\nabla h(X^*)$ is perpendicular to the constraint surface by definition.

In $\mathbb{R}^n$, the normal space to a single constraint surface is one-dimensional, there is only one independent normal direction. **So both vectors must lie along the same line**. That means one is a scalar multiple of the other:

$$
\begin{align} \nabla f(X^*) = -\lambda \, \nabla h(X^*) \\ \\ \nabla f(X^*) + \lambda \, \nabla h(X^*) = 0 \end{align}
$$

The scalar $\lambda$ is the **Lagrange multiplier**. And this condition is called the **KKT stationarity condition.**

#### What is the Lagrange multiplier ?

 The Lagrange multiplier $\lambda$ is the scalar that makes $\nabla f$ and $\nabla h$ balance. *But what does it mean?*

 Suppose I slightly relax the constraint, instead of $h(X) = 0$, I allow $h(X) = \epsilon$ for some tiny $\epsilon$. This is a **perturbation**, I've shifted the constraint surface by a small amount. The surface $h(X) = \epsilon$ is a slightly different surface from $h(X) = 0$, sitting nearby in $\mathbb{R}^n$.

Since the surface has shifted, the optimal point $X^*$ shifts too. I call this new optimal point $X(\epsilon)$, its a function of $\epsilon$:
- At $\epsilon = 0$: the constraint is the original one, so $X(0) = X^*$
- At $\epsilon \neq 0$: the constraint shifted, so the optimal point shifted too.

Now I'm asking: What is the rate at which the optimal value of $f$ changes as I relax the constraint $\epsilon$, or how does $f(X(\epsilon))$ change as $\epsilon$ increases from 0? That is, what is:
$$
\frac{d}{d\epsilon} f(X(\epsilon)) \Bigg|_{\epsilon = 0}
$$

Two facts I know
- The constraint is always satisfied, $h(X(\epsilon)) = \epsilon \quad \forall \ \epsilon$
- KKT stationarity holds at $X(0) = X^*$, i.e. $\nabla f(X^*) = -\lambda \, \nabla h(X^*)$

Taking the first fact and differentiating both sides,
$$
\begin{align} \nabla h(X(\epsilon))^T \cdot \frac{dX}{d\epsilon} = 1 \\ \nabla h\big(X(0)\big)^T \cdot \frac{dX}{d\epsilon}\Bigg|_{\epsilon=0} = 1  \end{align}
$$

 Finally I can differentiate $f(X(\epsilon))$ by applying the chain rule.  $X(\epsilon)$ is a vector-valued function of the scalar $\epsilon$, so:

$$
\frac{d}{d\epsilon} f(X(\epsilon)) = \nabla f(X(\epsilon))^T \cdot \frac{dX}{d\epsilon}
$$

This says: the rate of change of $f$ equals the gradient of $f$ (evaluated at the current optimal point) dotted with the *velocity $\frac{dX}{d\epsilon}$, the rate at which the optimal point itself is moving as $\epsilon$ changes.*

At $\epsilon = 0$, the point $X(\epsilon)$ is just $X^*$.

$$
\frac{d}{d\epsilon} f(X(\epsilon))\Bigg|_{\epsilon=0} = \nabla f\big(X(0)\big)^T \cdot \frac{dX}{d\epsilon}\Bigg|_{\epsilon=0}
$$

Substituting, $\nabla f(X(0)) = -\lambda \, \nabla h(X(0))$.
$$\begin{align} \frac{d}{d\epsilon} f(X(\epsilon))\Bigg|_{\epsilon=0} &= \big(-\lambda \, \nabla h(X^*)\big)^T \cdot \frac{dX}{d\epsilon}\Bigg|_{\epsilon=0} \\ \\ &= -\lambda \left( \nabla h(X^*)^T \cdot \frac{dX}{d\epsilon}\Bigg|_{\epsilon=0} \right) \\ \\ \frac{d}{d\epsilon} f(X(\epsilon))\Bigg|_{\epsilon=0} &= -\lambda \end{align}$$

 The rate at which the optimal value of $f$ changes as I relax the constraint is exactly $-\lambda$:
$$
\frac{d\, f(X(\epsilon))}{d\epsilon} = -\lambda
$$
**The Lagrange multiplier $\lambda$ is literally the negative of the rate at which the optimal value responds to constraint relaxation.**

So $\lambda$ is a **sensitivity**. Its sometimes called a **shadow price** in economics, it is the cost of the constraint. Relaxing a budget constraint by one unit changes the maximum profit by exactly $|\lambda|$. The multiplier is the price I'm paying for the rule being in place.

###### Large $|\lambda|$: The Constraint is a Tight Prison

Suppose $|\lambda|$ is large. Then $\frac{df^*}{d\epsilon} = -\lambda$ is large in magnitude. *A tiny perturbation $\epsilon$ causes a dramatic change in the optimal value.*

Geometrically, this means the constraint surface is cutting through a region where $f$ has steep contours. The constraint is **actively redirecting me away from the unconstrained minimum** by a large amount.

###### Small $|\lambda|$: The Constraint Barely Bites

When $|\lambda|$ is small, $\frac{df^*}{d\epsilon} \approx 0$. Perturbing the constraint barely moves the optimal value. There are two reasons this can happen:
- The constraint surface nearly passes through the unconstrained minimum.  The constraint isn't forcing me far from where I'd naturally go anyway. Loosening it slightly doesn't help much, because I wasn't being held far from my ideal point to begin with.
- The contours of $f$ near the constraint are very flat. Even if the constraint surface shifts, $f$ doesn't change much along the normal direction, so the optimal value barely moves. The objective is insensitive to this constraint's direction.

In both cases, $\lambda$ being small signals: *this constraint is not doing much work*.

###### $\lambda = 0$: The Constraint Is Redundant

When $\lambda = 0$, the stationarity condition $\nabla f(X^*) + \lambda \nabla h(X^*) = 0$ becomes simply $\nabla f(X^*) = 0$. That means $X^*$ was already a stationary point of $f$ on its own, the constraint happened to pass through the unconstrained minimum, or the unconstrained minimum happened to lie exactly on the constraint surface.

The constraint has zero sensitivity: $\frac{df^*}{d\epsilon} = 0$. I could shift the constraint surface slightly in either direction, and the optimal value of $f$ wouldn't change to first order. The constraint is technically satisfied, but it is **geometrically inert**, it is not shaping where the optimum lands.

#### The Lagrangian: One Function to Rule Them All

Carrying the equation $\nabla f + \lambda \nabla h = 0$ as a separate side condition is clumsy. There's a cleaner way to package everything.
Define the **Lagrangian function**:

$$
L(X, \lambda) = f(X) + \lambda \, h(X)
$$
Now I take its gradient with respect to $X$ and set it to zero:

$$
\nabla_X L = \nabla f(X) + \lambda \, \nabla h(X) = 0
$$

That's the **gradient alignment condition**, automatically. Now I take its derivative with respect to $\lambda$ and set it to zero:

$$
\frac{\partial L}{\partial \lambda} = h(X) = 0
$$

That's the constraint, automatically. The Lagrangian has absorbed both conditions into one object. Instead of solving a constrained problem in $X$, I now look for a **stationary point** of $L$ in the joint space $(X, \lambda)$. The constraint hasn't disappeared, it has been **encoded as an extra variable**.

This is the Lagrangian's entire job. It is not magic. It is just bookkeeping.

## Inequality Constraints

With $h(X) = 0$, I was glued to a surface with no freedom. An inequality constraint $g(X) \leq 0$ is different, I have a whole *region to move in*, and only the boundary $g(X) = 0$ is where things get tight. Two genuinely different situations arise at the optimum:

- **Case 1: The constraint is inactive: $g(X^*) < 0$**
The optimal point is strictly inside the feasible region, away from the boundary. The constraint isn't touching the solution at all. In this case, the unconstrained gradient condition $\nabla f(X^*) = 0$ holds on its own  —I'm at a free minimum inside the region. The constraint contributes nothing, so its multiplier must be $\mu = 0$. The full equation $\nabla f + \mu \nabla g = 0$ holds trivially.

- **Case 2: The constraint is active: $g(X^*) = 0$**

The optimal point is on the boundary. The same geometric argument from before applies: $\nabla f(X^*)$ must be parallel to $\nabla g(X^*)$, and $\mu > 0$. The constraint is genuinely shaping the solution.

These two cases together can be written as one compact condition:

$$
\mu \cdot g(X^*) = 0
$$

Either $g(X^*) = 0$ (active boundary) or $\mu = 0$ (inactive, irrelevant). One of them is always zero. This is called **complementary slackness** — and it's one of the four KKT conditions.

###### The Sign of $\mu$ Matters

For equality constraints, $\lambda$ had no sign restriction — the surface has no "inside" or "outside", so the normal $\nabla h$ can push in either direction.

Inequality constraints are different. The feasible region is one-sided: I'm inside $g(X) \leq 0$, and the constraint pushes me back *inward* when I'm pressed against the boundary. The gradient $\nabla g$ at the boundary points *outward* — away from the feasible region. For $\nabla f$ to be balanced by $\mu \nabla g$, the correction must point inward too, meaning it must oppose $\nabla g$. That requires:

$$
\mu \geq 0
$$

If $\mu$ were negative, it would mean $\nabla f$ is pointing inward — which means I could step inward and decrease $f$, contradicting the claim that I'm at a minimum. So **non-negativity of the inequality multiplier is not a convention — it is a geometric necessity**.

***

###### The KKT Conditions: The Complete Checklist

Putting it all together: for a point $X^*$ to be a local minimum of $f$ subject to constraints $g_i(X) \leq 0$ and $h_j(X) = 0$, there must exist multipliers $\mu_i \geq 0$ and $\lambda_j \in \mathbb{R}$ such that all four of the following hold simultaneously:

**1. Stationarity**
$$
\nabla f(X^*) + \sum_i \mu_i \nabla g_i(X^*) + \sum_j \lambda_j \nabla h_j(X^*) = 0
$$
The gradient of the Lagrangian vanishes at the optimum — no allowed move can improve $f$.

**2. Primal Feasibility**
$$
g_i(X^*) \leq 0 \quad \text{and} \quad h_j(X^*) = 0
$$
The solution must actually obey the rules. A point that violates any constraint is not a valid answer.

**3. Dual Feasibility**
$$
\mu_i \geq 0
$$
Inequality multipliers must be non-negative. Equality multipliers $\lambda_j$ are unconstrained in sign.

**4. Complementary Slackness**
$$
\mu_i \cdot g_i(X^*) = 0 \quad \forall i
$$
Each inequality constraint is either active (on the boundary, $\mu_i > 0$) or inactive (strictly inside, $\mu_i = 0$). Never both conditions non-zero simultaneously.

These are the **Karush-Kuhn-Tucker (KKT) conditions**. They are *necessary* conditions — any optimal point must pass this checklist, assuming the constraint gradients are well-behaved at $X^*$ (more on this shortly). Passing the checklist doesn't automatically mean I've found a minimum — I've found a *candidate*. Confirming it requires one more step.

***

###### Confirming It's Really a Minimum: Second-Order Conditions

The KKT conditions tell me *where* to look. They're like narrowing a suspect list — I know the answer is on the list, but I still need to verify. The tool for verification is the **Hessian of the Lagrangian**:

$$
\nabla_X^2 L(X^*;\, \boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)
$$

This is the matrix of all second derivatives of $L$ with respect to $X$. It describes the *curvature* of the Lagrangian — whether the surface is bowl-shaped (curving upward, a minimum) or dome-shaped (curving downward, a maximum) or saddle-shaped (neither).

But I can't check all of $\mathbb{R}^n$. I'm confined to the constraint surface. So I restrict attention to the **tangent cone** $T$ — the set of directions I can actually move in while respecting all active constraints:

$$
T = \{ d \in \mathbb{R}^n \mid \nabla g_i(X^*)^T d = 0 \; \forall \text{ active } i, \;\; \nabla h_j(X^*)^T d = 0 \; \forall j \}
$$

The **second-order sufficient condition** for a local minimum is:

$$
d^T \nabla_X^2 L(X^*;\, \boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)\, d > 0 \quad \text{for all } d \in T,\; d \neq 0
$$

In plain language: the Lagrangian curves upward in every direction I'm free to move. I'm at the bottom of a bowl, not the top of a hill or a saddle. If this holds only within the tangent cone $T$, it confirms a **local minimum**. If it holds for *all* non-zero directions in $\mathbb{R}^n$ (a stronger condition), it confirms a more robust minimum.

***

###### When I Don't Need to Check the Hessian

There is one beautiful case where the KKT conditions are both necessary *and* sufficient, and the Hessian check becomes unnecessary: **convex problems**.

A problem is convex when:
- $f$ is a convex function (bowl-shaped — no local minima that aren't global)
- Each $g_i$ is a convex function
- Each $h_j$ is a linear (affine) function

In this case the feasible set $\mathcal{F}$ is itself convex, and any local minimum is automatically a global minimum. There are no false summits. If a point satisfies the KKT conditions on a convex problem, it is the answer — no further checking needed.

***

## The Saddle Point: A Unified View

There is one final perspective that ties everything together elegantly. The optimal triple $(X^*, \boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)$ is a **saddle point** of the Lagrangian:

$$
L(X^*, \boldsymbol{\mu}, \boldsymbol{\lambda})
\leq
L(X^*, \boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)
\leq
L(X, \boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)
$$

The left inequality says: at fixed $X^*$, the Lagrangian is *maximized* over the multipliers at $(\boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)$. The right inequality says: at fixed multipliers $(\boldsymbol{\mu}^*, \boldsymbol{\lambda}^*)$, the Lagrangian is *minimized* over $X$ at $X^*$. The same point is simultaneously a minimum in one direction and a maximum in the other — like the center of a horse saddle, which is the lowest point along the horse's spine but the highest point left-to-right.

This gives rise to the **dual problem**: instead of minimizing $f$ over $\mathcal{F}$ (the primal), I can instead *maximize the Lagrangian* over the multipliers subject to $\nabla_X L = 0$ and $\boldsymbol{\mu} \geq 0$. When the problem is convex and a mild condition called **Slater's condition** holds (there exists at least one strictly feasible point), the primal and dual optimal values are identical — **strong duality**. This equivalence is what makes algorithms like Support Vector Machines tractable: the dual form of the SVM problem has a much simpler structure than the primal.