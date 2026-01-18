---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Probability]]

---

## Random Variable

From a probability standpoint, a random variable is a function that assigns a single real number to each possible outcome in the sample space of a random experiment.
*It's a rule for converting the (often non-numerical) results of an experiment into numbers.*

>[!cite] Probabilistic definition of a Random Variable
>A random variable $X$ is a function that maps outcomes to numbers. $X: \Omega \rightarrow \mathbb{R}$


The measure-theoretic definition formalizes this idea, making it more rigorous and powerful. It adds one crucial condition: **the function must be measurable.**
A **random variable** is a [[Measure Theory#^def-measurable-function|measurable function]] from a probability space to a measurable space of real numbers.

>[!cite] Random Variable
>A random variable $X$ on a probability space $(\Omega, \mathcal{F}, P )$ is a Borel measurable function from $\Omega$ to $\mathbb{R}$.
>
>A function $X: \Omega \rightarrow \mathbb{R}$ is a random variable if, for every set $B$ in the **[[Borel Sigma Algebra#^def-borel-algebra|Borel sigma-algebra]]** $\mathcal{B}(\mathbb{R})$ its **pre-image** is an event in the $\sigma$-algebra $\mathcal{F}$.
>
>$$\{\omega \in \Omega ∣ X(\omega) \in B\} \in \mathcal{F}$$

^def-random-variable

### The measurability Condition

Random Variable should be a measurable function. This "measurability" condition guarantees that any sensible question I can ask about the _value_ of the random variable $X$ corresponds to a valid _event_ in the original sample space, which I can then assign a probability to.

>[!example]
>I ask, What is the probability that $X$ is less than or equal to $5$ ?
>- The set of outcomes $B$ is the interval $(-\infty, 5]$.
>- This interval is in the Borel $\sigma$-algebra, i.e *the interval is a Borel set.*
>- The measurability condition ensures that the set of all original outcomes $\omega$ that $X$ maps into this interval is a well-defined event in $\mathcal{F}$.
>- Otherwise, I couldn't use my probability measure $P$ on it.

When I define a random variable $X: \Omega \rightarrow \mathbb{R}$ as a measurable function, I require that for every Borel set $B \in \mathcal{B}(\mathbb{R})$, the pre-image $X^{-1}(B)$ is measurable in the original space.
This ensures that questions like:
- What's $P(X ≤ 5)$ ?,  where ${(-\infty,5]} \in \mathcal{B}(\mathbb{R})$
- What's $P(2 < X ≤ 7)$ ?, where ${(2,7]} \in \mathcal{B}(\mathbb{R})$
- What's $P(X \in \mathbb{Q})$ ?, where $\mathbb{Q} \in \mathcal{B}(\mathbb{R})$

