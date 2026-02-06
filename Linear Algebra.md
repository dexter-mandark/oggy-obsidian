---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Vector Space

>[!cite] Group
>A **group** is a set $G$ together with a **binary** operation $\cdot$ (often written as $+$ or $\times$) that satisfies four axioms:
>1. **Closure**: For any $a, b \in G$, the result $a \cdot b$ is also in $G$
>2. **Associativity**: For all $a, b, c \in G$: $(a \cdot b) \cdot c = a \cdot (b \cdot c)$
>3. **Identity Element**: There exists an element $e \in G$ such that $a \cdot e = e \cdot a = a$ for all $a \in G$
>4. **Inverse Elements**: For each $a \in G$, there exists an element $b \in G$ (called $a^{-1}$) such that $a \cdot b = b \cdot a = e$
>
>If the operation is **commutative** (i.e., $a \cdot b = b \cdot a$ for all $a, b \in G$), the group is called an **Abelian group**.
>
>Examples:
>- Integers under addition $(\mathbb{Z}, +)$: The identity is $0$, and the inverse of any integer $a$ is $-a$
>- Non-zero real numbers under multiplication $(\mathbb{R}^*, \times)$: The identity is $1$, and the inverse of $a$ is $1/a$
^def-group

>[!cite] Field
>A **field** is a set $F$ with **two operations** (addition $+$ and multiplication $\times$) that satisfy the following properties:
>1. **Closure**: $F + F \rightarrow F$
>2. $(F, +)$ is an Abelian group with identity element $0$
>3. $(F \setminus \{0\}, \times)$ is an Abelian group with identity element $1$ (non-zero elements form a group under multiplication).
>4. **Distributivity**: Multiplication distributes over addition, for all $a, b, c \in F$, $a \times (b + c) = (a \times b) + (a \times c)$
>
>Examples:
>- Rational numbers $\mathbb{Q}$: I can add, subtract, multiply, and divide (except by zero) and stay within $\mathbb{Q}$
>- Real numbers $\mathbb{R}$: Same operations work.
>- Complex numbers $\mathbb{C}$: Includes imaginary numbers; still a field.
>- Finite fields: like $\mathbb{Z}_p$ (integers modulo a prime $p$)
^def-field


>[!cite] Vector Space
>Let $F$ be a field with identity elements $0$ and $1$. A **vector space** $V$ over a field $F$ is a set whose elements (called **vectors**) can be added together and multiplied by elements of $F$ (called **scalars**), subject to the following axioms:
>1. **Axioms for Vector Addition**: For all vectors $\mathbf{u}, \mathbf{v}, \mathbf{w} \in V$:
>	1. Closure under addition: $\mathbf{u} + \mathbf{v} \in V$
>	2. Commutativity: $\mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u}$
>	3. Associativity: $(\mathbf{u} + \mathbf{v}) + \mathbf{w} = \mathbf{u} + (\mathbf{v} + \mathbf{w})$
>	4. Additive identity: There exists a zero vector $\mathbf{0} \in V$ such that $\mathbf{v} + \mathbf{0} = \mathbf{v}$ for all $\mathbf{v} \in V$
>	5. Additive inverse: For each $\mathbf{v} \in V$, there exists $-\mathbf{v} \in V$ such that $\mathbf{v} + (-\mathbf{v}) = \mathbf{0}$
>
>2. **Axioms for Scalar Multiplication:**  For all scalars $c, d \in F$ and vectors $\mathbf{u}, \mathbf{v} \in V$:
>	1. Closure under scalar multiplication: $c\mathbf{v} \in V$
>	2. Distributivity (scalar over vector addition): $c(\mathbf{u} + \mathbf{v}) = c\mathbf{u} + c\mathbf{v}$
>	3. Distributivity (vector over scalar addition): $(c + d)\mathbf{v} = c\mathbf{v} + d\mathbf{v}$
>	4. Associativity of scalar multiplication: $c(d\mathbf{v}) = (cd)\mathbf{v}$
>	5. Multiplicative identity: $1\mathbf{v} = \mathbf{v}$, where $1$ is the multiplicative identity in $F$
>
^def-vector-space

>[!warning] Group vs Fields vs Vector Spaces
>Group: a set with an operation.
>Field: a set with two operations.
>Vector space: two sets
A field 