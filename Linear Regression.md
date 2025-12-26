---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Linear Regression

In the general [[Regression#^def-regression-problem|regression problem]], the goal is to learn _any_ function $\hat{f}:R^{d} \rightarrow R$.
In **linear regression**, I constrain this function to be a **linear function**. I assume that the true underlying relationship between the inputs and the output is linear.

$$y^{i} = \theta_0 + \theta_{1} x_{1}^{i}+ \theta_2 x_{2}^{i} + \ldots + \theta_d x_{d}^{i} + \epsilon_i$$

And the function (model) to predict  $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$, takes a specific form:

$$\hat{f}(\mathbf{x}; \boldsymbol{\theta}) = \theta_0 + \theta_1 x_1 + \theta_2 x_2 + \ldots + \theta_d x_d$$

- The parameters to be learned are the coefficients $\boldsymbol{\theta} = (\theta_0, \theta_1, \ldots, \theta_d)$
	- $\theta_0$ is the **bias (intercept)**
	- $\theta_1, \ldots, \theta_{d}$ are the **weights (coefficients)** for each feature.

- For the Loss function $L$: the standard choice is **Mean Squared Error (L2 loss)** $$L(y,\hat{y})=(y−\hat{y})^2$$

*I'm basically trying to predict the hyperplane that best approximates all the data-points in a $d$-dimensional space.


>[!abstract] Getting to the vector notation
>
>I can re-define $\hat f(x;\theta)$ and keep $x_0 = 1$, $$\hat f(x;\theta)=\sum_{j=0}^d \theta_j x_j$$
>
>Instead of the feature vector $\mathbf{x} \in \mathbb{R}^{d}$, I use an augmented feature vector $\tilde{\mathbf{x}} \in \mathbb{R}^{d+1}$, $$\tilde{\mathbf{x}} = \begin{bmatrix}  1 \\ x_1 \\ x_2 \\ \vdots \\ x_{d} \end{bmatrix}_{(d+1) \times 1} \quad \theta = \begin{bmatrix} \theta_0 \\ \theta_{1} \\\theta_2 \\ \vdots \\ \theta_{d} \end{bmatrix}_{(d+1) \times 1}$$
>
>Now, I can rewrite $\hat f(x;\theta)$ as a dot product of $\tilde{\mathbf{x}}$ and $\mathbf{\theta}$, $$\hat f(\mathbf{x};\mathbf{\theta})=\sum_{j=0}^{d} \theta_j x_{j} =\mathbf{\theta}^{T} \cdot \tilde{\mathbf{x}}= \tilde{\mathbf{x}} \cdot \mathbf{\theta}$$

Given the dataset $({(x^{(i)},y^{(i)})}_{i=1}^n)$, I can make the **design (data) matrix** $(\tilde X\in\mathbb{R}^{n\times(d+1)})$ whose $(i)$-th row is $(\tilde{\mathbf{x}}^{i})^{T}$:

$$\tilde X = \begin{bmatrix} 1 & x^{(1)}_1 & x^{(1)}_2 & \cdots & x^{(1)}_d \\ 1 & x^{(2)}_1 & x^{(2)}_2 & \cdots & x^{(2)}_d \\ \vdots & \vdots & \vdots & & \vdots \\ 1 & x^{(n)}_1 & x^{(n)}_2 & \cdots & x^{(n)}_d \end{bmatrix}$$

Let $Y$ be the vector of targets and $\theta\in\mathbb{R}^{d+1}$ the parameter vector. The predictions vector is,

$$\boxed{\large\hat Y = \tilde X \theta}$$

where $\hat y_i=(\tilde{\mathbf{x}}^{(i)})^{T} \cdot \theta=\hat f(\mathbf{x^{(i)}};\theta)$

## Ordinary Least Squares

This specific objective function (cost function) that linear regression tries to minimize is  the **Mean Squared Error (MSE)**.
The process of finding the parameters $\boldsymbol{\theta}$ that minimize the MSE is called **Ordinary Least Squares (OLS)**, also called **Ordinary Squared Error (OSE)**.

#### Optimizing Simple Linear Regression

>[!cite] Simple Linear Regression (1-D)
>
>The goal is to learn a function $f: \mathbb{R}^{1} \rightarrow \mathbb{R}^{1}$ that maps a single input $x$ to a single output $y$. Then $\hat{f}(\mathbf{x}; \mathbf{\theta})$ is nothing but a line. $$\hat{f}(\mathbf{x}; \mathbf{\theta}) = \theta_{1}x + \theta_{0}$$
>
>Where $(\theta_{0}, \theta_{1})$ are the two parameters I need to learn,
>- $\theta_{0}$ is the **intercept** of the line.
>- $\theta_{1}$ is the **slope** of the line.​


Give the function, $\hat{y}_i = \hat{f}(x_i; \boldsymbol{\theta}) = \theta_0 + \theta_1 x_i$
I want to find parameters $\boldsymbol{\theta}$ that minimize the sum of squared errors (SSE). So my cost function is:

$$J(\theta_0, \theta_1) = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2  = \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_i)^2$$

To minimize $J(\theta_{0}, \theta_{1})$, I take partial derivatives of $J$  with respect to $\theta_0$ and $\theta_1$, set them to zero.

###### Derivative with respect to $\theta_{0}$ :

$$\begin{align} \frac{\partial J}{\partial \theta_0}  &= \frac{\partial\big[ \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_{i})^{2} \big]}{\partial \theta_{0}} \\ & \implies \sum_{i=1}^{n} \frac{\partial (y_i -\theta_0 - \theta_1 x_{i})^{2}}{\partial \theta_{0}} = 0 \\  \\ & \implies  \sum_{i=1}^{n} 2(y_i -\theta_0 - \theta_1 x_{i})(-1) = 0 \\ & \implies  \sum_{i=1}^{n}y_{i} - n\theta_{0} - \theta_{1}\sum_{i=1}^{n}x_{i} = 0 \\ & \implies  \bar{y} - \theta_{0}- \theta_{1} \bar{x}  = 0 \\ \\ & \theta_{0}= \bar{y} - \theta_{1} \bar{x} \end{align}$$

###### Derivative with respect to $\theta_{1}$ :

$J$ is minimized when $\theta_{0}= \bar{y} - \theta_{1} \bar{x}$. I find the derivative with respect to $\theta_{1}$, keeping this value of $\theta_0$ :

$$\begin{align} \frac{\partial J}{\partial \theta_{1}}  &= \frac{\partial\big[ \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_{i})^{2} \big]}{\partial \theta_{1}} \\ & \implies \sum_{i=1}^{n} \frac{\partial \big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]^{2}}{\partial \theta_{0}} = 0 \\ \\ & \implies \sum_{i=1}^{n} 2\big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]\big[\bar{x} - x_{i} \big] = 0 \\ & \implies \sum_{i=1}^{n}\big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]\big[ x_{i} - \bar{x} \big] = 0 \\ & \implies \sum_{i=1}^{n}\big[(y_{i} -\bar{y}) - \theta_{1}(x_{i} - \bar{x})\big]\big[ x_{i} - \bar{x} \big] = 0 \\  & \implies \sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x}) = \sum_{i=1}^{n}\theta_{1}(x_{i} - \bar{x})^{2} \\ \\ & \theta_{1} = \frac{\sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x})}{\sum_{i=1}^{n}(x_{i} - \bar{x})^{2}} \end{align}$$

###### Final Result

The two parameters for linear regression are:

$$ \begin{align} \large \theta_{0} &= \bar{y} - \theta_{1} \bar{x} \\ \\
\theta_{1} &= \frac{\sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x})}{\sum_{i=1}^{n}(x_{i} - \bar{x})^{2}} = \frac{\text{Cov}(x, y)}{\text{Var}(x)}  \end{align}$$

#### Optimizing Multi-variate Linear Regression

###### Define the Variables

Let $X$ be my data (design) matrix such that each $x^{(i)} \in\mathbb{R}^{d+1}$

$$X = \begin{bmatrix} 1 & x^{(1)}_1 & x^{(1)}_2 & \cdots & x^{(1)}_d \\ 1 & x^{(2)}_1 & x^{(2)}_2 & \cdots & x^{(2)}_d \\ \vdots & \vdots & \vdots & & \vdots \\ 1 & x^{(n)}_1 & x^{(n)}_2 & \cdots & x^{(n)}_d \end{bmatrix}_{(d+1)\times n}$$

Let $Y$ be the vector of targets (true value), $\hat{Y}$ be the vector of predictions, and $\boldsymbol{\theta}$ be the vector of parameters. Where $\hat{Y} = X\boldsymbol{\theta}$. 

$$Y=\begin{bmatrix} y_{1} \\ y_{2} \\ \vdots \\ y_{n}\end{bmatrix}_{n \times 1} \quad  \hat{Y}=\begin{bmatrix} \hat{y}_{1} \\ \hat{y}_{2} \\ \vdots \\ \hat{y}_{n} \end{bmatrix}_{n \times 1} \quad  \boldsymbol{\theta}=\begin{bmatrix} \theta_{0} \\ \theta_{1} \\ \theta_{2} \\ \vdots \\ \theta_d\end{bmatrix}_{(d+1) \times 1} $$


###### Cost function
The cost function is the sum of squared errors (SSE), which I gotta minimize:

$$ \boxed{J(\boldsymbol{\theta}) = \sum_{i=1}^{n} (y_{i} - \hat{y}_{i})^{2} = \Big{\|} Y - \hat{Y}\Big{\|}^{2}_{2}} $$

That's nothing but the squared L2 distance between the two vector $Y$ and $\hat{Y}$,  and as both are $n$-dimensional vectors, the squared L2 distance is nothing but the dot product i.e. $Y^{T} \cdot \hat{Y}$ or $\hat{Y}^{T} \cdot Y$

$$\begin{align} J(\boldsymbol{\theta}) &= \Big{\|} Y - \hat{Y}\Big{\|}^{2}_{2} \\ \\  &=\Big{\|} Y - X\boldsymbol{\theta} \Big{\|}^{2}_{2} \\ \\ &= (Y - X\boldsymbol{\theta})^{T}(Y - X\boldsymbol{\theta}) \end{align}$$

I can expand this, using the transpose property $(A-B)^T = A^T - B^T$ and $(AB)^{T} = B^{T} A^{T}$:

$$\begin{align} J(\boldsymbol{\theta}) &= (Y^{T} - (\mathbf{X}\boldsymbol{\theta})^{T}) (Y - \mathbf{X}\boldsymbol{\theta}) \\ \\ &= (Y^{T} - \boldsymbol{\theta}^{T}\mathbf{X}^{T}) (Y - \mathbf{X}\boldsymbol{\theta}) \\ \\ &= Y^{T}Y - Y^{T}\mathbf{X}\boldsymbol{\theta} - \boldsymbol{\theta}^{T}\mathbf{X}^{T}Y + \boldsymbol{\theta}^{T}\mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta} \end{align}$$

Here, $\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y$ is a scalar, and $(\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y)^{T} = Y^{T}(\mathbf{X}^{T})^{T}(\boldsymbol{\theta}^{T})^{T} = Y^T\mathbf{X}\boldsymbol{\theta}$, so both the middle terms are scalar (transpose of a scalar is the scalar itself). So, I can combine the two. Also, $\mathbf{X}^{T}\mathbf{X}$ is symmetric.

$$\boxed{J(\boldsymbol{\theta}) = Y^{T}Y - 2\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y + \boldsymbol{\theta}^{T}\mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta}}$$
###### Gradient of the cost function 

To minimize the cost function $J(\boldsymbol{\theta})$, I compute the gradient $\nabla_{\theta}J(\boldsymbol{\theta})$.
Using the properties of gradient:
- $\nabla_{X}(A^{T} X)=A$
- $\nabla_{X}(X^{T} A)=A$
- $\nabla_{X}(X^{T} A X)=(A+A^{T})X$

$$\begin{align} \nabla_{\theta} J(Y^{T}Y)  &= 0 \\ \\ \nabla_{\theta} J( 2\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y) &= 2(\mathbf{X}^{T}Y)\\ \\ \nabla_{\theta} J(\boldsymbol{\theta}^{T} \mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta}) &= (\mathbf{X}^{T}\mathbf{X} + (\mathbf{X}^{T}\mathbf{X})^{T} )\boldsymbol{\theta} = 2\mathbf{X}^{T}\mathbf{X} \boldsymbol{\theta}\end{align}$$

And finally,
$$\boxed{\begin{align} \nabla_{\theta}J(\boldsymbol{\theta}) &= 2\mathbf{X}^{T}\mathbf{X} \boldsymbol{\theta} - 2\mathbf{X}^{T}Y \\ \\ &= 2\mathbf{X}^{T}(Y - \mathbf{X}{\boldsymbol{\theta}}) \end{align}}  $$
###### Final Result
Let the value of $\boldsymbol{\theta}$ that minimized the cost function $J(\boldsymbol{\theta})$ be $\hat{\boldsymbol{\theta}}$, by setting the gradient to zero, and multiplying both sides by $(\mathbf{X}^{T}\mathbf{X})^{-1}$ to isolate $\hat{\boldsymbol{\theta}}$,I get:

$$\begin{align} 2\mathbf{X}^{T}\mathbf{X} \hat{\boldsymbol{\theta}} &- 2\mathbf{X}^{T}Y = 0 \\ \\ \mathbf{X}^{T}\mathbf{X} \hat{\boldsymbol{\theta}} &= \mathbf{X}^{T}Y \\  \\ (\mathbf{X}^{T}\mathbf{X})^{-1}\mathbf{X}^{T}\mathbf{X} \hat{\boldsymbol{\theta}} &= (\mathbf{X}^{T}\mathbf{X})^{-1}\mathbf{X}^{T}Y\end{align}$$
$$\boxed{\hat{\boldsymbol{\theta}} = (\mathbf{X}^{T}\mathbf{X})^{-1}\mathbf{X}^{T}Y} $$

###### Normal Equation

The equation $\mathbf{X}^{T}\mathbf{X}\hat{\boldsymbol{\theta}} = \mathbf{X}^{T}Y$ can be rearranged as $\mathbf{X}^{T}(Y - \mathbf{X}\hat{\boldsymbol{\theta}}) = \mathbf{0}$

The term $Y - \mathbf{X}\hat{\boldsymbol{\theta}}$  is the **residual vector** (the error between actual and predicted values). And this equation states that $\mathbf{X}^T$ times the residual vector equals zero, which means **the residual vector is orthogonal (perpendicular) to every column of the design (data) matrix $\mathbf{X}$.​**

And that's why this equation is called the normal equation.

## Nothing but projection, Baby!

The cost function I use is $J(\boldsymbol{\theta}) = \Big{\|} Y - \mathbf{X}\boldsymbol{\theta} \Big{\|}^{2}_{2}$

To minimize this cost, what I'm basically doing is $\rightarrow$ find a $\hat{\boldsymbol{\theta}}$ $\rightarrow$ to make the predictions $\hat{Y}$ (or $\mathbf{X}\hat{\boldsymbol{\theta}}$)  as close as possible to the observed data $Y$ (the minimum L2 distance).

Each column of ($\mathbf{X}$) is a $d$-dimensional vector. The **span** (set of all linear combinations) of these columns forms a subspace of $\mathbb{R}^{d}$, which is nothing but $\text{Col}(\mathbf{X})$, the **column space** of $\mathbf{X}$. *Any prediction $\hat{Y} = \mathbf{X}\hat{\boldsymbol{\theta}}$ must lie in $\text{Col}(\mathbf{X})$*, because it's literally a linear combination of the columns of $\mathbf{X}$.

The actual values $Y$ (the true observed values) typically does not lie in $\text{Col}(\mathbf{X})$. This is because real data has noise, measurement error, and effects from variables not in my model. The true $Y$ exists somewhere in the full $n$-dimensional space $\mathbb{R}^{n}$, and not necessarily on the lower-dimensional subspace $\mathbb{R}^{d}$ defined by my features.

I have to find a $\hat{Y}$ in $\text{Col}(\mathbf{X})$ that is closest to $Y$ -- the **orthogonal projection of $Y$ onto $\text{Col}(\mathbf{X})$.** 

>[!cite] Orthogonal Projection
>The orthogonal projection of $y$ onto a subspace $\mathcal{S}$ is the **unique** point $\hat{y}\in\mathcal{S}$ such that the vector from $\hat{y}$ to $y$ that is ($y - \hat{y}$) is orthogonal to the subspace (orthogonal to every vector in $\mathcal{S}$).
>
>Let $\mathcal{S} \subseteq \mathbb{R}^n$ be a linear subspace, and let $y \in \mathbb{R}^n$ be any vector. Then there exists a **unique vector** $\hat{y} \in \mathcal{S}$ such that: $$\|y - \hat{y}\|_2 = \min_{z \in \mathcal{S}} \|y - z\|_2$$
>
>That is — *the orthogonal projection of $y$ onto $\mathcal{S}$ is the unique point in $\mathcal{S}$ closest to $y$.* 
>
>If I chose any other point $\tilde y$ in $\mathcal{S}$ that's not the orthogonal projection, the distance $\|y - \tilde{y}\|_2$ would be larger than $\|y - \hat{y}\|_2$

Because $\hat{Y}$is the orthogonal projection of $Y$ onto $\text{Col}(\mathbf{X})$, the **residual vector**: $r = Y - \hat{Y}$ is **orthogonal** to every column of $\mathbf{X}$. Mathematically:
$$X^{T} (Y - X\hat{\theta}) = 0$$

That’s exactly the **normal equation** I derived when $J(\boldsymbol{\theta})$ was minimized.

>[!tip] Linear Regression - Projection POV
>- The matrix $X$ defines a subspace of possible prediction vectors.
>- The actual data $Y$ might not lie in that subspace.
>- Linear regression finds the projection of $Y$ onto that subspace — i.e. the closest point to $Y$ that can be expressed as $X\theta$
>
>Thus:
>- $\hat{Y} \in \text{Col}(X)$
>- $r = Y - \hat{Y}$ is orthogonal to $\text{Col}(X)$
>
>*The fitted line/plane/hyperplane in regression is the projection surface that minimizes the perpendicular distance from $Y$.*


