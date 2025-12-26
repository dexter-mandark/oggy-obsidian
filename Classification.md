---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Classification Problem

A classification problem is a supervised learning task where the goal is to predict a discrete class label (categorical output) rather than a continuous numerical value.

>[!cite] Classification Problem
> Given a sample dataset of $n$ values $\mathcal{D} = \{(\mathbf{x}^{(i)}, y^{(i)})\}_{i=1}^{n}$, where $\mathbf{x}^{(i)} \in \mathbb{R}^d$ represents the input feature vector and $y^{(i)} \in \{C_1, C_2, ..., C_k\}$ represents the discrete class label. I assume there exists a **true (unknown) conditional probability distribution**:
>
> $$P^*(y | \mathbf{x})$$
>
> that describes the probability of observing class $y$ given input $\mathbf{x}$.
> The observed labels may be subject to **label noise** (mis-classification, ambiguous cases), meaning the true label assignment is inherently uncertain or stochastic.​
>
> The classification problem is to **find the optimal parameters that minimize the expected loss** over the data distribution. For a parametric model $\hat{f}(\mathbf{x}; \boldsymbol{\theta})$ that outputs class predictions, this is formulated as:
>
> $$\boldsymbol{\theta}^* = \arg\min_{\boldsymbol{\theta}} \sum_{i=1}^{n} L\left(\hat{y}^{(i)}, y^{(i)}\right)$$
> where,
> - $\hat{y}^{(i)}$ is the **predicted class label**: $\hat{y}^{(i)} = \hat{f}(\mathbf{x}^{(i)}; \boldsymbol{\theta})$
> - $L$ is a **loss function** measuring the discrepancy between predictions and observed class labels.
> 
> I try to come up with a parametric model that tries to approximate,
> $$P(y|\mathbf{x};\boldsymbol{\theta}) \approx P^{*}(y | \mathbf{x})$$
>
^def-classification-problem

The fundamental distinction from [[Regression#^def-regression-problem|regression problem]] is that $y^{(i)}$ takes values from a *finite discrete set* rather than a continuous real-valued range.

#### Probabilistic Modelling

Assuming all samples are **independent and identically distributed** the **likelihood function** of parameters $\boldsymbol{\theta}$ is:
$$L(\theta) = P(\mathcal{D} | \theta)  = \prod_{i=1}^n P(y^{(i)} | x^{(i)}; \theta)$$
This measures how “probable” the observed data is under the model with parameters $\boldsymbol{\theta}$.

I want to choose parameters that maximize this likelihood:
$$\theta^* = \arg\max_\theta L(\theta)  = \arg\max_\theta \prod_{i=1}^n P(y^{(i)} | x^{(i)}; \theta)  $$


Since products of probabilities can get extremely small, I take the **log-likelihood** instead (log is monotonic):

$$\begin{align} \ell(\theta) = \log L(\theta)  = \sum_{i=1}^n \log P(y^{(i)} | x^{(i)}; \theta) \\ \\ \boxed{\theta^* = \arg\max_\theta \sum_{i=1}^n \log P(y^{(i)} | x^{(i)}; \theta)}\end{align}$$

In most model formulations, I **minimize a loss** rather than maximize likelihood. So, I define the **loss** as the _negative log-likelihood_:
$$L(\hat{y}^{(i)}, y^{(i)}) = -\log P(y^{(i)} | x^{(i)}; \theta)$$

Then minimizing average loss is the same as maximizing the likelihood:

$$\boxed{\theta^* = \arg\min_\theta \sum_{i=1}^n L(\hat{y}^{(i)}, y^{(i)}) = \arg\max_\theta \sum_{i=1}^n \log P(y^{(i)} | x^{(i)}; \theta)}$$

