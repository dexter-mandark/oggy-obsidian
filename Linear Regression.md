---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Regression Problem

The goal of regression is to model the relationship between an **input vector** $\mathbf{x} \in \mathbb{R}^{d}$ and a **scalar output** $y \in \mathbb{R}$, learning a function $f: \mathbb{R}^d \rightarrow \mathbb{R}$ that maps inputs to outputs.
>[!cite] Regression Problem
>
>Given a sample dataset of $n$ examples $\mathcal{D} = \{(\mathbf{x}^{(i)}, y^{(i)})\}_{i=1}^{n}$, where $\mathbf{x}^{(i)} \in \mathbb{R}^{d}$ represents the input feature vector and $y^{(i)} \in \mathbb{R}$ represents the continuous target variable. I assume that there is some **true (unknown) function** $f^*(x)$ such that $$y = f^*(\mathbf{x}) + \varepsilon$$
>
 >where, $\varepsilon$ represents unobservable noise or error.
 >
>The regression problem is to **find the optimal parameters that minimize the empirical risk** (or cost) over the training set.
>For a parametric model $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$ this is formulated as: $$\boldsymbol{\theta}^* = \underset{\boldsymbol{\theta}}{\arg\min} \, \sum_{i=1}^{n} L\Big(y^{(i)}, \hat{y}^{(i)}\Big)$$
>
>where,
>- $\hat{y}^{(i)}$ is the **predicted value**: $\hat{y}^{(i)} =  \hat{f}(\mathbf{x}^{(i)}; \boldsymbol{\theta})$
>-  $L$ is a **loss function** measuring the discrepancy between predicted value $\hat{y}^{(i)}$ and true value $y^{(i)}$.
^def-regression-problem

#### Loss vs Cost

A **loss function** measures the error **for a single data point**. That’s the _individual_ penalty for getting one prediction wrong.
The **cost function** is the **average (or total)** of the losses over the entire dataset:

$$J(\theta) = \frac{1}{n} \sum_{i=1}^{n} L^{(i)}(\theta) $$

So the cost function aggregates all the individual losses.

## Linear Regression

In the general regression problem, the goal is to learn _any_ function $\hat{f}:\mathbb{R}^{d} \rightarrow \mathbb{R}$.
In **linear regression**, I constrain this function to be a **linear function**. I assume that the true underlying relationship between the inputs and the output is linear.

###### The Setup

For a data point $(\mathbf{x}^{(i)}, y^{(i)})$, where $\mathbf{x}^{(i)} = (x_1^{(i)}, x_2^{(i)}, x_3^{(i)}, \ldots x_{d}^{(i)})$ is the feature vector.
I run under the assumption that there exists a linear relationship between $\mathbf{x}^{(i)}$ and $y^{(i)}$, with some additive noise $\epsilon_i$ written as:
$$y^{(i)} = \theta_0 + \theta_{1} x_{1}^{(i)}+ \theta_2 x_{2}^{(i)} + \ldots + \theta_d x_{d}^{(i)} + \epsilon_i$$

The predictive model $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$, is therefore defined as:

$$\hat{f}(\mathbf{x}; \boldsymbol{\theta}) = \theta_0 + \theta_1 x_1 + \theta_2 x_2 + \ldots + \theta_d x_d$$

- The model is parameterized (parameter to be learned) by the vector $\boldsymbol{\theta} \in \mathbb{R}^{d+1}$
	- $\theta_0$ is the **bias (intercept)**, representing the expected output when all feature values are zero.
	- $\theta_1, \ldots, \theta_{d}$ are the **weights (coefficients)**, where $\theta_j$ quantifies the change in the output for a unit change in feature $x_j$.

- For the Loss function $L$: the standard choice is **Mean Squared Error (L2 loss)** $$L(y,\hat{y})=(y−\hat{y})^2$$

###### Notation

To simplify the mathematical notation and implementation, I can absorb the bias term $\theta_0$ by augmenting $x_0 = 1$, this is the *"bias trick" (also known as augmentation)*.

Instead of the feature vector $\mathbf{x} \in \mathbb{R}^{d}$, I use an augmented feature vector $\tilde{\mathbf{x}} \in \mathbb{R}^{d+1}$, $$\tilde{\mathbf{x}} = \begin{bmatrix}  1 \\ x_1 \\ x_2 \\ \vdots \\ x_{d} \end{bmatrix}_{(d+1) \times 1} \quad \theta = \begin{bmatrix} \theta_0 \\ \theta_{1} \\\theta_2 \\ \vdots \\ \theta_{d} \end{bmatrix}_{(d+1) \times 1}$$
Now, I can rewrite $\hat f(\mathbf{x};\boldsymbol{\theta})$ as a dot product of $\tilde{\mathbf{x}}$ and $\boldsymbol{\theta}$, $$\hat f(\mathbf{x};\boldsymbol{\theta})=\sum_{j=0}^{d} \theta_j x_{j} =\boldsymbol{\theta}^{T} \cdot \tilde{\mathbf{x}}= \tilde{\mathbf{x}}^{T} \cdot \boldsymbol{\theta}$$

Given the dataset $\mathcal{D} = \{(\mathbf{x}^{(i)},y^{(i)})\}_{i=1}^n$, I can make the **design (data) matrix** $\tilde X\in\mathbb{R}^{n\times(d+1)}$ whose $(i)$-th row is $(\tilde{\mathbf{x}}^{(i)})^{T}$:

$$\tilde X = \begin{bmatrix} 1 & x^{(1)}_1 & x^{(1)}_2 & \cdots & x^{(1)}_d \\ 1 & x^{(2)}_1 & x^{(2)}_2 & \cdots & x^{(2)}_d \\ \vdots & \vdots & \vdots & & \vdots \\ 1 & x^{(n)}_1 & x^{(n)}_2 & \cdots & x^{(n)}_d \end{bmatrix}$$

Let $\hat{Y} \in \mathbb{R}^n$ be the vector of predictions for all samples. I can compute all predictions simultaneously via matrix-vector multiplication:
$$\hat Y = \tilde X \cdot \boldsymbol{\theta}$$

Here $\hat{y}^{(i)}$, the $i$-th element of $\hat{Y}$ corresponds to the prediction for the $i$-th sample: $$\hat y^{(i)}=\hat f(\mathbf{x^{(i)}};\boldsymbol{\theta})=(\tilde{\mathbf{x}}^{(i)})^{T} \cdot \boldsymbol{\theta}$$

_Geometric Interpretation:_ The aim to find the hyperplane in the $(d+1)$-dimensional space that best approximates the data points by minimizing the vertical distances (residuals) between the observed points and the plane.

## Ordinary Least Squares

This specific objective function (cost function) that linear regression tries to minimize is the **Mean Squared Error (MSE)**.
The process of finding the parameters $\boldsymbol{\theta}$ that minimize the MSE is called **Ordinary Least Squares (OLS)**, also called **Ordinary Squared Error (OSE)**.

#### Optimizing Simple Linear Regression

>[!cite] Simple Linear Regression (1-D)
>
>The goal is to learn a function $f: \mathbb{R}^{1} \rightarrow \mathbb{R}^{1}$ that maps a single input $x$ to a single output $y$. Then $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$ is nothing but a line. $$\hat{f}(\mathbf{x}; \boldsymbol{\theta}) = \theta_{1}x + \theta_{0}$$
>
>Where $(\theta_{0}, \theta_{1})$ are the two parameters I need to learn,
>- $\theta_{0}$ is the **intercept** of the line.
>- $\theta_{1}$ is the **slope** of the line.​

For the data point $(x_i, y_i)$, let the prediction be $\hat{y}_i= \theta_0 + \theta_1 x_i$. Then $e_i = y_i − \hat{y}_i$ represents the $i$-th **residual** (the error).
I want to find parameters $\theta_0, \theta_1$ that minimize the **sum of squared errors (SSE)**, also called as **residual sum of squares (RSS)**. So my cost function is:

$$J(\theta_0, \theta_1) = \sum_{i=1}^n e_i^2 =  \sum_{i=1}^{n} (y_i - \hat{y}_i)^2  = \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_i)^2$$

To minimize $J(\theta_{0}, \theta_{1})$, I take partial derivatives of $J$  with respect to $\theta_0$ and $\theta_1$, set them to zero.

###### Derivative with respect to $\theta_{0}$ :

$$\begin{align} \frac{\partial J}{\partial \theta_0}  &= \frac{\partial\big[ \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_{i})^{2} \big]}{\partial \theta_{0}}  = 0\\ & \implies \sum_{i=1}^{n} \frac{\partial (y_i -\theta_0 - \theta_1 x_{i})^{2}}{\partial \theta_{0}} = 0 \\  & \implies  \sum_{i=1}^{n} 2(y_i -\theta_0 - \theta_1 x_{i})(-1) = 0 \\ & \implies  \sum_{i=1}^{n}y_{i} - n\theta_{0} - \theta_{1}\sum_{i=1}^{n}x_{i} = 0 \\ \\ & \implies  n\bar{y} - n\theta_{0}- \theta_{1} n\bar{x}  = 0 \\ \\ & \implies  \bar{y} - \theta_{0}- \theta_{1} \bar{x}  = 0 \\ \\ & \theta_{0}= \bar{y} - \theta_{1} \bar{x} \end{align}$$

###### Derivative with respect to $\theta_{1}$ :

$J$ is minimized when $\theta_{0}= \bar{y} - \theta_{1} \bar{x}$. I find the derivative with respect to $\theta_{1}$, keeping this value of $\theta_0$ :

$$\begin{align} \frac{\partial J}{\partial \theta_{1}}  &= \frac{\partial\big[ \sum_{i=1}^{n} (y_i -\theta_0 - \theta_1 x_{i})^{2} \big]}{\partial \theta_{1}} \\ & \implies \sum_{i=1}^{n} \frac{\partial \big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]^{2}}{\partial \theta_{0}} = 0 \\ \\ & \implies \sum_{i=1}^{n} 2\big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]\big[\bar{x} - x_{i} \big] = 0 \\ & \implies 2 \sum_{i=1}^{n}\big[y_i -(\bar{y} - \theta_{1} \bar{x}) - \theta_1 x_{i}\big]\big[ x_{i} - \bar{x} \big] = 0  \\ & \implies \sum_{i=1}^{n}\big[(y_{i} -\bar{y}) - \theta_{1}(x_{i} - \bar{x})\big]\big[ x_{i} - \bar{x} \big] = 0 \\ & \implies \sum_{i=1}^{n}\big[(y_{i} -\bar{y})(x_{i} - \bar{x}) - \theta_{1}(x_{i} - \bar{x})^2 \big] = 0 \\  & \implies \sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x}) - \sum_{i=1}^{n}\theta_{1}(x_{i} - \bar{x})^{2} = 0 \\ \\ & \implies \sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x}) = \sum_{i=1}^{n}\theta_{1}(x_{i} - \bar{x})^{2} \\ \\ & \theta_{1} = \frac{\sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x})}{\sum_{i=1}^{n}(x_{i} - \bar{x})^{2}} \end{align}$$

###### Final Result

The two parameters for simple linear regression are:

$$ \begin{align} \theta_{0} &= \bar{y} - \theta_{1} \bar{x} \\ \\
\theta_{1} &= \frac{\sum_{i=1}^{n}(y_{i} -\bar{y})(x_{i} - \bar{x})}{\sum_{i=1}^{n}(x_{i} - \bar{x})^{2}} = \frac{\text{Cov}(\mathbf{x}, \mathbf{y})}{\text{Var}(\mathbf{x})}  \end{align}$$

#### Optimizing Multi-variate Linear Regression

###### Define the Variables

Let $X$ be my data (design) matrix such that each $x^{(i)} \in\mathbb{R}^{d+1}$

$$\mathbf{X} = \begin{bmatrix} 1 & x^{(1)}_1 & x^{(1)}_2 & \cdots & x^{(1)}_d \\ 1 & x^{(2)}_1 & x^{(2)}_2 & \cdots & x^{(2)}_d \\ \vdots & \vdots & \vdots & & \vdots \\ 1 & x^{(n)}_1 & x^{(n)}_2 & \cdots & x^{(n)}_d \end{bmatrix}_{n \times (d+1)}$$

Let $Y$ be the vector of targets (true value), $\hat{Y}$ be the vector of predictions, and $\boldsymbol{\theta}$ be the vector of parameters. Where $\hat{Y} = X\boldsymbol{\theta}$. 

$$Y=\begin{bmatrix} y^{(1)} \\ y^{(2)} \\ \vdots \\ y^{(n)}\end{bmatrix}_{n \times 1} \quad  \hat{Y}=\begin{bmatrix} \hat{y}^{(1)} \\ \hat{y}^{(2)} \\ \vdots \\ \hat{y}^{(n)} \end{bmatrix}_{n \times 1} \quad  \boldsymbol{\theta}=\begin{bmatrix} \theta_{0} \\ \theta_{1} \\ \theta_{2} \\ \vdots \\ \theta_d\end{bmatrix}_{(d+1) \times 1} $$


###### Cost function
The cost function is the sum of squared errors (SSE), which I gotta minimize:

$$ J(\boldsymbol{\theta}) = \sum_{i=1}^{n} (y^{(i)} - \hat{y}^{(i)})^{2} = \Big{\|} Y - \hat{Y}\Big{\|}^{2}_{2}$$

That's nothing but the squared [[Norm#^def-euclidean-distance|L2 distance]] between the two vector $Y$ and $\hat{Y}$,  and the squared L2 distance for a vector $V$ is nothing but the $V^{T} \cdot V$

$$\begin{align} J(\boldsymbol{\theta}) &= \Big{\|} Y - \hat{Y}\Big{\|}^{2}_{2} \\ \\  &=\Big{\|} Y - \mathbf{X}\boldsymbol{\theta} \Big{\|}^{2}_{2} \\ \\ &= (Y - \mathbf{X}\boldsymbol{\theta})^{T}(Y - \mathbf{X}\boldsymbol{\theta}) \end{align}$$

I can expand this, using the transpose property, $(A-B)^T = A^T - B^T$ and $(AB)^{T} = B^{T} A^{T}$:

$$\begin{align} J(\boldsymbol{\theta}) &= (Y^{T} - (\mathbf{X}\boldsymbol{\theta})^{T}) (Y - \mathbf{X}\boldsymbol{\theta}) \\ \\ &= (Y^{T} - \boldsymbol{\theta}^{T}\mathbf{X}^{T}) (Y - \mathbf{X}\boldsymbol{\theta}) \\ \\ &= Y^{T}Y - Y^{T}\mathbf{X}\boldsymbol{\theta} - \boldsymbol{\theta}^{T}\mathbf{X}^{T}Y + \boldsymbol{\theta}^{T}\mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta} \end{align}$$

Here, $\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y$ is a scalar, and $(\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y)^{T} = Y^{T}(\mathbf{X}^{T})^{T}(\boldsymbol{\theta}^{T})^{T} = Y^T\mathbf{X}\boldsymbol{\theta}$, so both the middle terms are scalar (transpose of a scalar is the scalar itself). So, I can combine the two. Also, $\mathbf{X}^{T}\mathbf{X}$ is symmetric.

$$J(\boldsymbol{\theta}) = Y^{T}Y - 2\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y + \boldsymbol{\theta}^{T}\mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta}$$
###### Gradient of the cost function 

To minimize the cost function $J(\boldsymbol{\theta})$, I compute the gradient $\nabla_{\theta}J(\boldsymbol{\theta})$.
Using the properties of gradient:
- $\nabla_{X}(A^{T} X)=A$
- $\nabla_{X}(X^{T} A)=A$
- $\nabla_{X}(X^{T} A X)=(A+A^{T})X$

$$\begin{align} \nabla_{\theta} J(Y^{T}Y)  &= 0 \\ \\ \nabla_{\theta} J( 2\boldsymbol{\theta}^{T}\mathbf{X}^{T}Y) &= 2(\mathbf{X}^{T}Y)\\ \\ \nabla_{\theta} J(\boldsymbol{\theta}^{T} \mathbf{X}^{T}\mathbf{X}\boldsymbol{\theta}) &= (\mathbf{X}^{T}\mathbf{X} + (\mathbf{X}^{T}\mathbf{X})^{T} )\boldsymbol{\theta} = 2\mathbf{X}^{T}\mathbf{X} \boldsymbol{\theta}\end{align}$$

And finally,
$$\nabla_{\theta}J(\boldsymbol{\theta}) = 2\mathbf{X}^{T}\mathbf{X} \boldsymbol{\theta} - 2\mathbf{X}^{T}Y$$
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

Each column of ($\mathbf{X}$) is a $(d+1)$-dimensional vector. The **span** (set of all linear combinations) of these columns forms a subspace of $\mathbb{R}^{d+1}$, which is nothing but $\text{Col}(\mathbf{X})$, the **column space** of $\mathbf{X}$. *Any prediction $\hat{Y} = \mathbf{X}\hat{\boldsymbol{\theta}}$ must lie in $\text{Col}(\mathbf{X})$*, because it's literally a linear combination of the columns of $\mathbf{X}$.

The actual values $Y$ (the true observed values) typically does not lie in $\text{Col}(\mathbf{X})$. This is because real data has noise, measurement error, and effects from variables not in my model. The true $Y$ exists somewhere in the full $n$-dimensional space $\mathbb{R}^{n}$, and not necessarily on the lower-dimensional subspace $\mathbb{R}^{d+1}$ defined by my features.

I have to find a $\hat{Y}$ in $\text{Col}(\mathbf{X})$ that is closest to $Y$ -- the **orthogonal projection of $Y$ onto $\text{Col}(\mathbf{X})$.** 

>[!cite] Orthogonal Projection
>The orthogonal projection of $y$ onto a subspace $\mathcal{S}$ is the **unique** point $\hat{y}\in\mathcal{S}$ such that the vector from $\hat{y}$ to $y$, that is ($y - \hat{y}$), is orthogonal to the subspace (orthogonal to every vector in $\mathcal{S}$).
>
>Let $\mathcal{S} \subseteq \mathbb{R}^n$ be a linear subspace, and let $y \in \mathbb{R}^n$ be any vector. Then there exists a **unique vector** $\hat{y} \in \mathcal{S}$ such that: $$\|y - \hat{y}\|_2 = \min_{z \in \mathcal{S}} \|y - z\|_2$$
>
>That is — *the orthogonal projection of $y$ onto $\mathcal{S}$ is the unique point in $\mathcal{S}$ closest to $y$.* 
>
>If I chose any other point $\tilde y$ in $\mathcal{S}$ that's not the orthogonal projection, the distance $\|y - \tilde{y}\|_2$ would be larger than $\|y - \hat{y}\|_2$

Because $\hat{Y}$ is the orthogonal projection of $Y$ onto $\text{Col}(\mathbf{X})$, the **residual vector**: $r = Y - \hat{Y}$ is **orthogonal** to every column of $\mathbf{X}$. Mathematically:
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


