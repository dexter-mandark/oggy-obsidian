---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Norm

Norm is a function that maps a vector from a real or complex vector space to a non-negative real number and satisfies a specific set of axioms.
Norm *assigns a strictly positive length or size to each vector* in a vector space, with the zero vector being the only exception, which has a length of zero.
A norm generalizes the intuitive concept of "length" or "magnitude" for vectors.

>[!cite] Norm of a Vector
>Let $V$ be a vector space over a field $K$, where $K$ is either the field $\mathbb{R}$ of reals, or the field $\mathbb{C}$ of complex numbers.
>A norm on $V$ is a function $\Vert . \Vert: V \rightarrow \mathbb{R}_{+}$ that satisfies the following conditions for all $x, y \in V$ and $\alpha \in K$ $\rightarrow$
>- **Non-negativity**: $\Vert x \Vert \geq 0$
>- **Positive definiteness** $\Vert x \Vert=0 \text{iff} x = 0$ 
>- **Absolute homogeneity** (or scalar multiplication): $\Vert \alpha x \Vert = |\alpha|\Vert x \Vert$
>- **Triangle inequality** $\Vert x+y \Vert \leq \Vert x \Vert + \Vert y \Vert$
>
>Norm assigns a non-negative real number $\Vert x \Vert$ to any vector $x \in V$,

###### Conditions for a Norm

- The length of any vector must be a non-negative number.
- Only the zero vector has a length of zero; all other vectors must have a strictly positive length.
- Scaling a vector by a scalar factor scales its length by the absolute value (or modulus) of that scalar.
- The length of the sum of two vectors is less than or equal to the sum of their individual lengths. This is a generalization of the geometric idea that the length of any side of a triangle cannot be greater than the sum of the lengths of the other two sides.

###### Normed Vector Space

A vector space that is equipped with a norm is called a **normed vector space**. Every norm on a vector space induces a metric, or a notion of distance, defined as $d(x,y)=\|x−y\|$.

#### L1 Norm

The **L1 norm**, also known as the **Manhattan norm** or **Taxicab norm**, measures the magnitude of a vector by summing the *absolute values of its components*.

The name "Manhattan norm" comes from the idea of measuring distance in a grid-like city layout, where you can only travel along horizontal or vertical streets.

>[!cite] L1 (Manhattan) Norm
>For a vector $x=[x_{1},x_{2},\cdots,x_{n}]$, the L1 Norm (denoted as $\| x \|_{1}$) is defined as $\rightarrow$
>
>$$\large{\|x\|_{1}} = \sum_{i=1}^{n}| x_{i}| = |x_{1}| + |x_{2}| \cdots | x_{n} |$$

^def-l1-norm

#### L2 Norm

The **L2 norm**, also known as the **Euclidean norm**, is the most common way to measure a vector's magnitude.
It calculates the straight-line distance from the origin to the point represented by the vector. This corresponds to the conventional understanding of length in Euclidean space.

>[!cite] L2 (Euclidean) Norm
>For a vector $x=[x_{1},x_{2},\cdots,x_{n}]$, the L2 Norm (denoted as $\| x \|_{2}$) is defined as $\rightarrow$
>
>$$\large{ \|x\|_{2} } = \sqrt{\sum_{i=1}^{n} x_{i}^{2}} = \sqrt{x_{1}^{2} + x_{2}^{2} \cdots x_{n}^{2} }$$

^def-l2-norm

#### $L_{p}$ Norm

I can generalize the idea of L1 and L2 norms, as the $L_{p}$ norm.
>[!cite] $L_{p}$ Norm
>The $L_{p}$-Norm of a vector $x=[x_{1},x_{2},\cdots,x_{n}]$, is denoted as $\| x \|_{p}$ and is defined as $\rightarrow$
>
>$$\large{ \|x\|_{p} } = \Big({\sum_{i=1}^{n} |x_{i}|^{p}}\Big)^{\frac{1}{p}} = \Big({|x_{1}|^{p} + |x_{2}|^{p} \cdots |x_{n}|^{p}}\Big)^{\frac{1}{p}}$$

^def-lp-norm

## Minkowski distance

The Minkowski distance is a *generalized distance metric in a normed vector space* that unifies several other common distance metrics, such as Euclidean and Manhattan distances. It provides a flexible method for calculating the distance between two points in an $n$-dimensional space by using a parameter, $p$, which determines the nature of the distance calculation.

>[!cite] Minkowski Distance
>The Minkowski distance of order $p$ between two points $X = [x_{1}, x_{2}, \cdots x_{n}]$ and  $Y = [y_{1}, y_{2}, \cdots y_{n}]$ in a $n$-dimensional normed vector space is defined as:
>
>$$\large D_{p}(X,Y)= (\sum_{i=1}^{n} \| x_{i}−y_{i} \|_{p})^{\frac{1}{p}}$$

^def-minkowski-distance

The Minkowski distance is a true metric for $p \geq 1$, satisfying these essential properties:
- **Positivity**: The distance is always non-negative and is zero only if the points are identical.
- **Symmetry**: The distance from $X$ to $Y$ is the same as the distance from $Y$ to $X$, meaning $D_{p}(X,Y) = D_{p}(Y,X)​$
- **Triangle Inequality**: The distance between two points is always less than or equal to the sum of the distances from each point to a third point, i.e. $D_{p}(X,Z) \leq D_{p}(X,Y) + D_{p}(Y,Z)$

#### Manhattan Distance

Manhattan distance is defined as the **L1 norm** of the difference between two vectors. It calculates distance by summing the absolute differences of the coordinates of the two points. This is also known as the _taxicab distance_ or _city block distance_ because it represents the path a taxi would take on a grid-like street layout, where it can only travel along horizontal and vertical lines.

>[!cite] Manhattan Distance
>For two $n$-dimensional vectors $p = [p_{1}, p_{2}, \cdots p_{n}]$ and  $q = [q_{1}, q_{2}, \cdots q_{n}]$.
>The Manhattan distance $D_{1}(p ,q)$ is defined as:
>
>$$D_{1}(p,q)= \| p−q \|_{1}= \sum_{i=1}^{n}|p_{i} - q_{i}| = |p_{1} - q_{1}| + |p_{2} - q_{2}| + \cdots + |p_{d} - q_{d}|$$

^def-manhattan-distance

#### Euclidean Distance

Euclidean distance is the length of the straight-line segment connecting two points in Euclidean space. It is the most common way to *represent the shortest distance between two points*.

![[euclidean distance in 2D .png|#invert_B|500]]

>[!cite] Euclidean Distance
>For two $n$-dimensional vectors $p = [p_{1}, p_{2}, \cdots p_{n}]$ and  $q = [q_{1}, q_{2}, \cdots q_{n}]$.
>The Euclidean distance between two vectors, $p$ and $q$, is calculated by finding the *L2 Norm of* the vector representing their *difference* $(p-q)= [(p_{1}-q_{1}), (p_{2}-q_{2}), \cdots, (p_{n} - q_{n})]$, that is $\rightarrow$
>
>$$D_{2}(p,q)= \| p−q \|_{2}= \sqrt{\sum_{i=1}^{n}(p_{i} - q_{i})^{2}} = \sqrt{(p_{1} - q_{1})^{2} + (p_{2} - q_{2})^{2} + \cdots + (p_{n} - q_{n})^{2}}$$

^def-euclidean-distance

>[!danger] No subscript
>If you don't see a subscript, assume Euclidean distance. $\| p -q \|$ is $D_{2}(p, q)$, the euclidean distance between $p$ and $q$.