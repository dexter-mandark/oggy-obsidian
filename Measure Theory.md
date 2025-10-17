---
cssclasses:
  - callouts-outlined
---

###### Tags: #measure-theory , #measure, #sigma-algebra, #probability-theory

---
**Measure theory** provides a systematic and rigorous way to assign "sizes" or "measures" to subsets of a given space. It can be applied to give a rigorous grounding to probability theory by resolving certain paradoxes.

Measure theory gives me three essential components:
- Which sets can I measure? (σ-algebras)
- How do I measure them? (measures)
- What are the rules for consistent measurement? (measure axioms)

*How can I systematically assign "sizes" to sets in a way that behaves nicely, mathematically?*

## Sigma Algebra

A **$\sigma$-algebra** is a special collection of subsets that have been "declared measurable", i.e. the official list of sets I'm allowed to measure in my space.


I can't measure every possible subset - some are too "pathological." A $\sigma$-algebra identifies the **well-behaved subsets** that can be measured consistently.

>[!quote] Sigma Algebra
Let $X$ be a non-empty set, $P(X)$ be the power set of $X$.
A collection of subsets $\sigma \subseteq P(X)$ is a $\sigma$-algebra for $X$ if $\rightarrow$
>- Contains the whole space: $X \in \sigma$
>- Closed under complements: $A \in \sigma \implies A^{C}_{\small{X}} \in \sigma$
>- Closed under countable unions: $\begin{align} A_{1}, A_{2}, A_{3} ... \in \sigma \implies & \bigcup_{i=1}^{\infty}A_{i} \in \sigma \end{align}$
>
>$A \in \sigma$ is called **measurable set** in $X$.
>*$\sigma$-algebra is a set of all measurable subsets.*
^def-sigma-algebra

### The three rules

All system of subsets that follow the three rules are $\sigma$-algebra.

###### Contains the whole space

*I should be able to measure the whole space.* The entire set $X$ should have a generalized volume.

###### Closed under Complement

*If I can measure a region, I should also measure "everything else" (its complement).*
If a generalized volume can be assigned to any element $A$ in $\sigma$, then I should be able to assign a generalized volume to the complement of that element with respect to $X$, i.e. $A^{C}_{\small{X}}$ should be an element of the $\sigma$ as well.

Using these two rules, I can also imply, $\phi \in \sigma$.

###### Closed under Countable Union

*If I can measure individual pieces, I should measure their combination.*
A finite or countable infinite union of measurable sets is also measurable.

$\sigma$-algebra is closed under countable unions and countable intersection of any sets.

### Trivial $\sigma$ -algebra

The **smallest possible** $\sigma$-algebra on any set $X$ is $\{ \phi, X\}$.
The **largest possible** is the power set $P(X)$ (all subsets).

$P(X)$ would be the most desirable $\sigma$-algebra. In general, however, $P(X)$ does not provide a useful $\sigma$-algebra for most cases (especially if $X$ is non-countable), thus most useful σ-algebras lie somewhere **between the two extremes**.

## Measurable Spaces

>[!quote] Measurable Space
>The **measurable space** $(X, \sigma)$:  a set $X$ with a $\sigma$-algebra $\sigma$, that tells me which sets I can measure.
>
>Given a measurable space $(X, \sigma)$, a subset $A \subseteq X$ is called **measurable** if and only if $A \in \sigma$.

I have a set $X$, and I have $\sigma$ on $X$
- Measurable sets: Sets in the σ-algebra $\rightarrow$ Sets I can measure.
- Non-measurable sets: Sets not in the σ-algebra $\rightarrow$ Sets I can't measure.

###### Key Insights

- **σ-algebra determines measurability**: The choice of $\sigma$-algebra determines which sets count as "measurable".
- **Context matters**: A set might be measurable with respect to one $\sigma$-algebra but not another
- **Closure properties**: Since $\sigma$-algebras are closed under unions, intersections, and complements, all these operations preserve measurability.

## Measure

Now what I do, is *assign each set* in the measurable space $(X, \sigma)$ *a numerical "size"* or the "generalized volume".

>[!abstract] Measure
>A measure is a function $\mu: \sigma \rightarrow \mathbb{R}_{0}^{+}$ that satisfies three axioms:
>- **Null Empty Set**: $\mu(\phi) = 0$
>- **Non-negativity:** $\mu(A) \geq 0 \ \forall \  A \in \sigma$
>- **Countable Additivity:** For any countable collection of *disjoint sets* $A_{1}, A_{2}, A_{3} ... \in \sigma \rightarrow$
>
>$$\large{\mu}(\bigcup_{i=1}^{\infty}A_{i}) = \sum_{i=1}^{\infty} \large{\mu}(A_{i})  \ \ \text{where} \ \ A_{i} \cap A_{j} = \phi \ \  \text{for} \ \ i \neq j $$
>
>The measure $\mu$ is a function that assigns a non-negative number (possibly infinite) to each measurable set.
>$\sigma$-algebra is nothing but the domain of this function $\mu$.
^def-measure

###### The three Axioms

- Axiom 1: "Empty set" has size zero
- Axiom 2: Sizes are never negative
- Axiom 3: When I combine non-overlapping pieces, total size should be equal to the sum of individual sizes

This third axiom is what makes measure theory work! It ensures my "measuring" behaves like I expect from length, area, volume, and probability.

## Measure Space

###### Just a Set $X$

I start with a basic set $X$ - this could be the real line $\mathbb{R}$, a collection of outcomes from an experiment, or any other set. I have *no structure* for measuring anything.

###### Measurable Space $(X, \sigma)$

Introducing a $\sigma$-algebra, gives me a *measurable space* $(X, \sigma)$
- **Which sets I can measure:** Only the sets in $\sigma$ are "measurable".
- **Structure for measurement:** The $\sigma$-algebra provides the rules (closure under unions, intersections, complements)
- **No actual measurement**: I know which sets are eligible, but not their "sizes".

###### Measure Space $(X, \sigma, \mu)$

Introducing a measure $\mu$ creates a **measure space** $(X, \sigma, \mu)$. This completes the framework because now I have:
- **Which sets I can measure**: $\sigma$-algebra
- **How to measure them**: $\mu$ assigns actual numerical "sizes" to each measurable set.

## Measurable Functions

Assuming I have two measurable spaces:
- A starting space: $(X, \mathcal{F})$
- A target space: $(Y, \mathcal{G})$

Here, $\mathcal{F}$ and $\mathcal{G}$ are the respective $\sigma$-algebras on the sets $X$ and $Y$.

>[!cite] Measurable Function
>A function $f: X \rightarrow Y$ is said to be **measurable** if for every measurable set $E$ in the target space's $\sigma$- algebra $\mathcal{G}$, its *pre-image is a measurable set* in the starting space's $\sigma$-algebra $\mathcal{F}$.
>That is, for every $E \in \mathcal{G}$, the [[Pre-Image#^def-pre-image|pre-image]] $f^{-1}(E) \in \mathcal{F}$.
>
>Here, the pre-image is defined as: $f^{-1}(E)=\{x \in X∣f(x) \in E\}$
^def-measurable-function

This "pre-image" condition is the key. 
- I can ask a question about the output of my function by choosing a measurable set $E$ in the target space $Y$ (e.g., "all values between 5 and 10").
- The measurability of the function guarantees that the set of all inputs $x$ that produce those outputs is a well-defined measurable set in my starting space $X$
- Because this pre-image is a measurable set in $X$, I can now apply a **measure** (like a probability measure) to it to find its "size."

Without this guarantee, I might ask a perfectly sensible question about the function's output, only to find that the corresponding set of inputs is one of the "pathological" non-measurable sets that I can't assign a size to. Measurable functions prevent this from ever happening.

*So, a **measurable function** is simply a function that is "well-behaved" with respect to the $\sigma$-algebras of its domain and codomain, ensuring that I can transfer questions about measure from one space to the other.*
