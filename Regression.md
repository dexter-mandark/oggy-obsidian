---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Regression Problem

The goal of regression is to model the relationship between an **input vector** $x \in \mathbb{R}^{d}$ and a **scalar output** $y \in \mathbb{R}$, to learn a function $f: \mathbb{R}^d \rightarrow \mathbb{R}$ that maps inputs to outputs.​

>[!cite] Regression Problem
>
>Given a sample dataset of $n$ values $\{(\mathbf{x}^{(i)}, y^{(i)})\}_{i=1}^{n}$, where $\mathbf{x}^{(i)} \in \mathbb{R}^{d}$ represents the input feature vector and $y^{(i)} \in \mathbb{R}$ represents the continuous target variable. I assume that there is some **true (unknown) function** $f^*(x)$ such that $$y = f^*(x) + \varepsilon$$
>
 >where, $\varepsilon$ represents unobservable noise or error.
>
>The regression problem is to **find the optimal parameters that minimize the expected loss** over the data distribution. For a parametric model $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$ this is formulated as: $$\boldsymbol{\theta}^* = \underset{\boldsymbol{\theta}}{\arg\min} \, \sum_{i=1}^{n} L\Big(y^{(i)}, \hat{y}^{(i)}\Big)$$
>
>where,
>- $\hat{y}^{(i)}$ is the **predicted value**: $\hat{y}^{(i)} =  \hat{f}(\mathbf{x}^{(i)}; \boldsymbol{\theta})$
>-  $L$ is a **loss function** measuring the discrepancy between predictions and true values.
^def-regression-problem

#### Loss vs Cost

A **loss function** measures the error **for a single data point**. That’s the _individual_ penalty for getting one prediction wrong.
The **cost function** is the **average (or total)** of the losses over the entire dataset:

$$J(\theta) = \frac{1}{n} \sum_{i=1}^{n} L^{(i)}(\theta) $$

So the cost function aggregates all the individual losses.

