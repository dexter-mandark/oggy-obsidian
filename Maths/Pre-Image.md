---
cssclasses:
  - callouts-outlined
---

###### Tags: [[Measure Theory]]

---

In simple terms, the pre-image is about "working backwards."
- An **image** is what I get when you apply a function: you start with an input and find its output. (Going forward).
- A **pre-image** is what you started with: you look at an output (or a set of outputs) and find all the inputs that could have produced it. (Going backward).

The pre-image is a set of all the "origins" or "sources" in the starting space (the domain) that map into a specific region of the target space (the co-domain).

>[!cite] Pre-Image
>For a function $f:A \rightarrow B$, the pre-image of a set $Y \subseteq B$ is denoted as $f^{-1}(Y)$ and is defined as:
>
>$$f^{-1}(Y) = {x \in A ∣ f(x) \in Y}$$
>
>The pre-image of $Y$ is the collection of all elements $x$ in the starting set $A$ whose corresponding output $f(x)$ is in the target set $Y$.
^def-pre-image

## Pre-image vs. Inverse Function

The notation $f^{-1}(x)$ is used for representing both, the inverse function and pre-image.
- An **inverse function** only exists if the function $f$ is *one-to-one and onto*. It maps a single output back to a single input.
- The **pre-image** $f^{-1}(x)$ is a **set operation** that is _always_ defined for _any_ function, regardless of whether it has an inverse. It maps a subset of the codomain back to a subset of the domain.

The function $f(x) = x^{2}$ does not have a true inverse function (because $f(2)$ and $f(-2)$ are both $4$), but I can always find the pre-image of any set, and here the pre-image $f^{-1}(4) = \{-2, 2\}$.

>[!danger]
>A **pre-image is a set operation**, not a function in the traditional sense. A key requirement for a function is that every input must map to exactly one output. The pre-image operation does not always satisfy this
