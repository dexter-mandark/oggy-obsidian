---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Perceptron

A perceptron is a **mathematical model that performs binary classification**—it takes multiple inputs and produces a single binary output, typically 0 or 1.

Its the simplest form of an artificial neural network and the fundamental building block for more complex networks. Conceived by Frank Rosenblatt in 1957.

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

## Nothing but a hyperplane

The perceptron *works by creating a linear decision boundary* that best separates the data into its two classes. The weights and bias are adjusted to minimize the error between the perceptron's predicted output and the actual output. The *equation for this boundary is where the net input is exactly zero*.​
$$\mathbf{w} \cdot \mathbf{x} + b = 0$$

This equation is nothing but a **hyperplane** in the $d$-dimensional input space. And if Heaviside step function is used as the activation function then, all the points which are
- above the hyperplane ($\mathbf{w} \cdot \mathbf{x} + b > 0$) are classified as class-$1$.
- below the hyperplane  ($\mathbf{w} \cdot \mathbf{x} + b \le 0$) are classified as class-$0$.


The **weight vector** is always **orthogonal (perpendicular) to the decision boundary**. It determines the orientation or slope of the hyperplane. Geometrically, the weight vector points directly into the region that the perceptron will classify as "1". Any input vector $\mathbf{x}$ that has a large positive projection onto $\mathbf{w}$ will result in a large positive net input, leading to a "1" classification.​

The **bias** term determines the **position of the boundary** relative to the origin. Without a bias term ($b=0$), the hyperplane would be forced to pass through the origin. The bias allows the decision boundary to be shifted, giving the model the flexibility to find a separating line or plane that does not go through the origin, which is essential for many real-world datasets.

#### Perceptron Learning Algorithm

For the training set be $\mathcal{D} = \{(\mathbf{x}_1, y_1), (\mathbf{x}_2, y_2), \ldots, (\mathbf{x}_N, y_N)\}$ where $\mathbf{x}_j$ is a $d$-dimensional input vector and $y_j \in \{0, 1\}$ is the corresponding true label. The goal of the algorithm is to find a suitable weight vector $\mathbf{w}$ that correctly classifies a set of labelled training examples.

###### Initialization

The weight vector $\boldsymbol{\theta}$ is initialized. This can be with zeros, ones, or small random numbers. Let the weight vector at iteration $t$ be $\boldsymbol{\theta}^{(t)}$.
I can *augment the bias* term directly into the input vector $\boldsymbol{\theta}$ and the net input $z$  can be expressed as a simple dot product of two vectors $\boldsymbol{\theta}$ and $\mathbf{x}$, that is $z = \mathbf{x} \cdot \boldsymbol{\theta}$, where
$$\mathbf{x} = \begin{bmatrix}  1 \\ x_1 \\ x_2 \\ \vdots \\ x_{d} \end{bmatrix}_{(d+1) \times 1} \quad \theta = \begin{bmatrix} \theta_0 \\ \theta_{1} \\\theta_2 \\ \vdots \\ \theta_{d} \end{bmatrix}_{(d+1) \times 1}$$

###### Iteration

The algorithm iterates through the training data. At each step, it takes a training example $(x_j,y_j)$ and calculates the predicted output $\hat{y}_j$, as:
$$\hat{y}_j = h(\boldsymbol{\theta}^{(t)} \cdot \mathbf{x}_j)$$
Where, $h$ is the Heaviside step function.

The **core is the update rule**, which is applied whenever a point is misclassified (i.e., when $y_j \neq \hat{y}_j$)
$$\boxed{\large \boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} + \eta (y_j - \hat{y}_j) \cdot \mathbf{x}_j}$$
Where,
- $\boldsymbol{\theta}^{(t+1)}$ is the new, updated weight vector.
- $\eta$ is the **learning rate**, a small positive scalar, that controls the magnitude of the weight adjustment.
- $(y_j - \hat{y}_j)$ is the error term. This will be either +1 or -1.
- $\mathbf{x}_j$ is the input vector of the misclassified point.
  
The process is repeated for a fixed number of iterations (**epochs**) or until all points in the training set are correctly classified.

###### Misclassified Points "Push" the Hyperplane

The update rule $\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} + \eta (y_j - \hat{y}_j) \cdot \mathbf{x}_j$, geometrically adjusts ()"pushes") the decision boundary $(\boldsymbol{\theta} \cdot \mathbf{x} = 0)$
The **weight vector $\boldsymbol{\theta}$ is always perpendicular to the decision boundary** and points towards the region that is classified as "1".

For the two cases of misclassification:
- **False Negative ($y = 1, \hat{y} = 0$)**: The point $\mathbf{x}_j$ is on the wrong side of the decision boundary, the angle between the vector $\boldsymbol{\theta}^{(t)}$ and the vector $\mathbf{x}_j$ is greater than 90 degrees (their dot product is negative), which is why it was classified as $0$. In this case the update rule is $\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} + \eta \ \mathbf{x}_j$, I add a fraction of the input vector to the weight vector. I'm basically *nudging the weight vector $\boldsymbol{\theta}$ to be more aligned with $\mathbf{x}_j$.* This operation pulls the tip of the $\boldsymbol{\theta}^{(t)}$ vector in the direction of $\mathbf{x}_j$.
- **False Positive ($y = 0, \hat{y} = 1$)**: In this case the update rule is $\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} - \eta \ \mathbf{x}_j$, I subtract a fraction of the input vector from the weight vector. I'm basically *nudging the weight vector $\boldsymbol{\theta}$ to be more away with $\mathbf{x}_j$.* This operation pushes the tip of the $\boldsymbol{\theta}^{(t)}$ vector away from the direction of $\mathbf{x}_j$, increasing the angle between them and making their dot product more likely to be negative in the future.

## This is why a single-layer perceptron can only solve problems that are **linearly separable**.