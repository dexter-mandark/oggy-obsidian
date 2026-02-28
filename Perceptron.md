---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Perceptron

A perceptron is a **mathematical model that performs binary classification**. It takes multiple inputs and produces a single binary output, typically 0 or 1.

Its the simplest form of an artificial neural network and the fundamental building block for more complex networks. Conceived by *Frank Rosenblatt* in 1957.

> [!cite] Perceptron
> A **perceptron** is defined as a function $f: \mathbb{R}^d \to \{0, 1\}$, where $\mathbb{R}^d$ is the space of $d$-dimensional input vectors.
> Given an input vector $\mathbf{x} \in \mathbb{R}^d$, a weight vector $\mathbf{w} \in \mathbb{R}^d$, and a scalar bias term $b \in \mathbb{R}$, perceptron function $f(\mathbf{x})$ is defined as:
> $$f(\mathbf{x}) = h(\mathbf{w} \cdot \mathbf{x} + b) = h(z)$$
> Where,
> - **Input Vector**: Each $\mathbf{x}$ has $d$-components $$\mathbf{x} = (x_1, x_2, \ldots, x_d)$$
> - **Weight vector**: Each weight $w_i$ represents the importance of the corresponding input $x_i$   $$\mathbf{w} = (w_1, w_2, \ldots, w_d)$$
> 
> - **Bias**: $b$ is the bias, a term that allows the decision boundary to be shifted away from the origin.
> - **Net Input**: $z$ is the net input or weighted sum, given as: $$z = (\mathbf{w} \cdot \mathbf{x}) + b = \left(\sum_{i=1}^{m} w_i x_i\right) + b$$
> - **Activation Function**: The net input $z$ is passed through an activation function $h(z)$ which is typically a non-linear step function. For the classic perceptron, this is the [[Definitions#^def-heaviside-step-function|Heaviside Step Function]].
> 
>The goal of the perceptron is to find a weight vector $\mathbf{w}$ and a bias $b$ such that the function $f(\mathbf{x})$ produces the correct output for as many examples in the training set as possible.

#### The Decision Boundary

The perceptron works by creating a **linear decision boundary** that best separates the data into its two classes. The weights and bias are adjusted to minimize the error between the perceptron's predicted output and the actual output. The *equation for this boundary is where the net input is exactly zero*.​
$$\mathbf{w} \cdot \mathbf{x} + b = 0$$
This equation is nothing but a **hyperplane** in the $d$-dimensional input space. And if Heaviside step function is used as the activation function then, all the points $\mathbf{x}_i$ which are
- above the hyperplane ($\mathbf{w} \cdot \mathbf{x}_i + b > 0$) are classified as class-$1$.
- below the hyperplane  ($\mathbf{w} \cdot \mathbf{x}_i + b \le 0$) are classified as class-$0$.

>[!abstract] Weight vector is perpendicular to the decision boundary
> The decision boundary is the set of all points $\mathbf{x}$ satisfying: $\mathbf{w}\cdot \mathbf{x} + b = 0$
> For any two points $\mathbf{x}_1$ and $\mathbf{x}_2$ that both lie on this boundary. By definition, each satisfies the equation:
> $$
> \begin{align} \mathbf{w}\cdot \mathbf{x}_1 + b &= 0
> \\
> \mathbf{w}\cdot \mathbf{x}_2 + b &= 0 \end{align}
> $$
> Now be a little clever with this and subtract the two equations:
>
> $$
> \mathbf{w}\cdot \mathbf{x}_1 - \mathbf{w}\cdot \mathbf{x}_2 = 0
> \;\Rightarrow\;
> \mathbf{w}\cdot (\mathbf{x}_1 - \mathbf{x}_2) = 0
> $$
>
> The vector $(\mathbf{x}_1 - \mathbf{x}_2)$ is a vector that points along the boundary (from one boundary point to another). Since its dot product with $\mathbf{w}$ is zero, $\mathbf{w}$ is perpendicular to it. Because this holds for any pair of points on the boundary, $\mathbf{w}$ is perpendicular to every direction along the boundary.

The **weight vector** is always **orthogonal (perpendicular) to the decision boundary**. It determines the orientation or slope of the hyperplane. Geometrically, the weight vector points directly into the region that the perceptron will classify as "1" (This is simply because $\nabla_{\mathbf{x}}(\mathbf{w}\cdot \mathbf{x} + b) = \mathbf{w}$, so $\mathbf{w}$ is the direction of fastest increase).
Any input vector $\mathbf{x}$ that has a large positive projection onto $\mathbf{w}$ will result in a large positive net input, leading to a "1" classification.​

The **bias** term determines the **position of the boundary** relative to the origin. Without a bias term ($b=0$), the hyperplane would be forced to pass through the origin. The bias allows the decision boundary to be shifted, giving the model the flexibility to find a separating line or plane that does not go through the origin, which is essential for many real-world datasets.

## Perceptron Learning Algorithm

For the training set be $\mathcal{D} = \{(\mathbf{x}_1, y_1), (\mathbf{x}_2, y_2), \ldots, (\mathbf{x}_N, y_N)\}$ where $\mathbf{x}_j$ is a $d$-dimensional input vector and $y_j \in \{0, 1\}$ is the corresponding true label. The goal of the algorithm is to find a suitable weight vector $\mathbf{w}$ that correctly classifies a set of labelled training examples.

###### Initialization

The weight vector $\mathbf{w}$ is initialized. This can be with zeros, ones, or small random numbers. Let the weight vector at iteration $t$ be $\mathbf{w}^{(t)}$.
I can *augment the bias* term directly into the input vector $\mathbf{w}$ and the net input $z$  can be expressed as a simple dot product of two vectors $\mathbf{w}$ and $\mathbf{x}$, that is $z = \mathbf{x} \cdot \mathbf{w}$, where
$$\mathbf{x} = \begin{bmatrix}  1 \\ x_1 \\ x_2 \\ \vdots \\ x_{d} \end{bmatrix}_{(d+1) \times 1} \quad w = \begin{bmatrix} w_0 \\ w_{1} \\w_2 \\ \vdots \\ w_{d} \end{bmatrix}_{(d+1) \times 1}$$

###### Iteration

The algorithm iterates through the training data. At each step, it takes a training example $(x_j,y_j)$ and calculates the predicted output $\hat{y}_j$, as:
$$\hat{y}_j = h(\mathbf{w}^{(t)} \cdot \mathbf{x}_j)$$
Where, $h$ is the Heaviside step function.
The **core is the update rule**, which is applied whenever a point is misclassified (i.e., when $y_j \neq \hat{y}_j$)
$$\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + \eta (y_j - \hat{y}_j) \cdot \mathbf{x}_j$$
Where,
- $\mathbf{w}^{(t+1)}$ is the new, updated weight vector.
- $\eta$ is the **learning rate**, a small positive scalar, that controls the magnitude of the weight adjustment.
- $(y_j - \hat{y}_j)$ is the error term. This will be either +1 or -1.
- $\mathbf{x}_j$ is the input vector of the misclassified point.
  
The process is repeated for a fixed number of iterations (**epochs**) or until all points in the training set are correctly classified.

###### Misclassified Points "Push" the Hyperplane

The update rule $\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + \eta (y_j - \hat{y}_j) \cdot \mathbf{x}_j$, geometrically adjusts ("pushes") the decision boundary. Just keep one basic thing in mind: **adding a vector to another rotates it toward that vector; subtracting rotates it away.**

For the two cases of mis-classification:
- **False Negative ($y = 1, \hat{y} = 0$)**: The point $\mathbf{x}_j$ is on the wrong side of the decision boundary, the angle between the vector $\mathbf{w}^{(t)}$ and the vector $\mathbf{x}_j$ is greater than $90^{\circ}$ (their dot product is negative), which is why it was classified as class-$0$. In this case the update rule becomes:	$$\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + \eta \ \mathbf{x}_j$$This operation pulls the tip of the $\mathbf{w}^{(t)}$ vector in the direction of $\mathbf{x}_j$. I'm basically *nudging the weight vector $\mathbf{w}$ to be more aligned with $\mathbf{x}_j$.*

- **False Positive ($y = 0, \hat{y} = 1$)**: In this case the update rule becomes: $$\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} - \eta \ \mathbf{x}_j$$This operation pushes the tip of the $\mathbf{w}^{(t)}$ vector away from the direction of $\mathbf{x}_j$, increasing the angle between them and making their dot product more likely to be negative in the future. I'm basically *nudging the weight vector $\mathbf{w}$ to be more away with $\mathbf{x}_j$.*

## The Novikoff Convergence Theorem

>[!cite] The Novikoff Convergence Theorem
>The Novikoff Convergence Theorem (1962) is the foundational guarantee of the perceptron: if the data is linearly separable, the perceptron algorithm will make at most a finite number of mistakes, regardless of the number of training examples or their dimensionality.
>
>The theorem states: If the data is linearly separable with margin $\gamma$ and all inputs have norm at most $R$, then the number of mistakes $t$ the perceptron makes is: $$t \le \left(\frac{R}{\gamma}\right)^2$$
^def-novikoff-convergence-theorem

###### The Setup

Assuming that the data is linearly separable, I define two key quantities about my dataset:
- The radius $R$: The largest norm of any input vector, $$\max_j \|\mathbf{x}_j\| \le R$$This bounds how “far out” my data points are.

- The margin $\gamma$: There exists an ideal unit weight vector $\mathbf{u}$ ($\|\mathbf{u}\|=1$) that correctly classifies every point with a gap of at least $\gamma>0$:$$y_j(\mathbf{u}\cdot \mathbf{x}_j) \ge \gamma \quad \forall\, j$$Every single class-1 point is at least $\gamma$ "above" the boundary, and every class-0 point is at least $\gamma$ "below" it. No point sits right on the edge or too close. The margin $\gamma$ measures how “comfortably” separable the data is, a large margin means the classes are far apart.

###### Proof !!!! FIX THIS THE FUCK IS YJ

The proof *tracks $\|\mathbf{w}^{(t)}\|$ after $t$ mistakes* and derives two bounds that force $t$ to be finite.

**Finding the Lower Bound:**
Every update happens only on a mistake, so each update is:
$$
\mathbf{w}^{(t)} = \mathbf{w}^{(t-1)} + y_j \mathbf{x}_j
$$
Taking dot product with the ideal vector $\mathbf{u}$ and substituting $y_j(\mathbf{u}\cdot \mathbf{x}_j) \ge \gamma$:
$$
\begin{align} \mathbf{u}\cdot \mathbf{w}^{(t)} &= \mathbf{u}\cdot \mathbf{w}^{(t-1)} + y_j(\mathbf{u}\cdot \mathbf{x}_j) \\ 
\mathbf{u}\cdot \mathbf{w}^{(t)} &\ge \mathbf{u}\cdot \mathbf{w}^{(t-1)} + \gamma \end{align}
$$
I with $\mathbf{w}^{(0)}=\mathbf{0}$. After $t$ mistakes (by induction):
$$
\mathbf{u}\cdot \mathbf{w}^{(t)} \ge t\gamma.
$$
By Cauchy–Schwarz (the norm of any vector is at least its projection onto any unit vector):
$$
\begin{align} \|\mathbf{u}\|\;\|\mathbf{w}^{(t)}\| &\ge \mathbf{u}\cdot \mathbf{w}^{(t)} \\ \\ \implies \|\mathbf{w}^{(t)}\| &\ge \mathbf{u}\cdot \mathbf{w}^{(t)}  \\ \\ \implies \|\mathbf{w}^{(t)}\| &\ge \mathbf{u}\cdot \mathbf{w}^{(t)} \ge t\gamma \\ \\ \implies \|\mathbf{w}^{(t)}\| &\ge t\gamma \end{align}
$$

**Finding the upper bound:**
I can just expand $\|\mathbf{w}^{(t)}\|^2$:
$$
\begin{aligned}
\|\mathbf{w}^{(t)}\|^2
&= \|\mathbf{w}^{(t-1)} + y_j\mathbf{x}_j\|^2 \\
&= \|\mathbf{w}^{(t-1)}\|^2 + 2y_j(\mathbf{w}^{(t)}\cdot \mathbf{x}_j) + \|\mathbf{x}_j\|^2
\end{aligned}
$$
The term $\|\mathbf{x}_j\|^2$ is bounded by $R^2$. And the term $y_j(\mathbf{w}^{(t-1)}\cdot \mathbf{x}_j)\le 0$ because the update only happens on a misclassified point. So:
$$\|\mathbf{w}^{(t)}\|^2 \le \|\mathbf{w}^{(t-1)}\|^2 + R^2$$
After $t$ mistakes:
$$
\|\mathbf{w}^{(t)}\|^2 \le tR^2
\implies
\|\mathbf{w}^{(t)}\| \le \sqrt{t}\,R
$$

**Combining the upper and lower bounds:**
$$
\begin{align} t\gamma &\le \|\mathbf{w}^{(t)}\| \le \sqrt{t}\,R \\
\implies
t\gamma &\le \sqrt{t}\,R
\implies
\sqrt{t} \le \frac{R}{\gamma} \\
\implies
t &\le \frac{R^2}{\gamma^2} \end{align}
$$
This basically, limits the number of iterations $t$ I can do. The algorithm **must** converge in at most $\frac{R}{\gamma}$ updates.
- Large margin  $\gamma$: Fewer mistakes, easy to learn.
- Large radius  $R$ (spread-out data): More mistakes, harder to learn.
- Number of examples $N$: No effect, the bound is independent of dataset size.
- Dimension $d$: No effect, the bound is independent of dimension.

###### Non-Uniqueness and Brittleness

The theorem proves convergence but **says nothing about which hyperplane you find**. The Perceptron *stops the moment it finds any valid separator*, and there are infinitely many. The one the perceptron algorithm lands on depends entirely on:​
- The **order** in which training points are visited
- The **initial weight** $\mathbf{w}^{(0)}$
- The **learning rate** $\eta$
