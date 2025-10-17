---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

>[!cite] Borel Sigma Algebra
>The Borel $\sigma$-algebra on $\mathbb{R}$ denoted $\mathcal{B}(\mathbb{R})$ is the **smallest [[Measure Theory#^def-sigma-algebra|sigma-algebra]]** that contains all the open sets of the real line.
^def-borel-algebra

It's named after the French mathematician Émile Borel.

### The Construction Process

Here's how it's built:
1. **Start with open sets**: Begin with all open intervals like $(a,b), (-\infty,c), (d,\infty)$ etc.
2. **Apply $\sigma$-algebra rules**: Take all possible countable unions, intersections, and complements of these open sets.
3. **Keep going**: Repeat this process indefinitely until you can't create any new sets
4. **Result**: The collection of all sets you can create this way is $\mathcal{B}(\mathbb{R})$.

The Borel  $\sigma$-algebra can also be described as the smallest $\sigma$-algebra containing:
- All **open intervals** $(a,b)$
- All **closed intervals** $[a,b]$
- All **half-open intervals** $(a,b]$ or $[a,b)$
- All **single points** $\{x\}$

These all generate the same σ-algebra.

### Why Borel Algebra

The Borel $\sigma$-algebra represents the mathematical compromise between:
- **Too little**: Having only a few measurable sets (limiting what we can measure)
- **Too much**: Including pathological non-measurable sets (breaking the mathematical framework)