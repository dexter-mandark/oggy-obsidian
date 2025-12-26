---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Logistic Regression

Logistic regression is a supervised learning algorithm for [[Classification#^def-classification-problem|classification problems]]. It models the conditional probability distribution of class membership given input features using a parametric probabilistic model.
Despite its name, it is actually used for **classification problems** rather than regression.

###### Variables

Given a dataset $\mathcal{D} = \{(\mathbf{x}^{(i)}, y^{(i)})\}_{i=1}^{n}$ where $\mathbf{x}^{(i)} \in \mathbb{R}^d$ and $y^{(i)} \in \{C_1, C_2, ..., C_K\}$.
Where,
- $y$ is a **random variable** that represents the class label. It can take any value from the set of classes$\{C_1, C_2, ..., C_K\}$
- I assume there exists a true conditional probability distribution $P^*(y|\mathbf{x})$
	- $P^∗(y=C_i∣\mathbf{x})$ gives me the probability that the random variable $y$, equals the specific class $C_i$, given the feature vector $\mathbf{x}$.
	- $P^*(y|\mathbf{x})$ defines probabilities for all possible classes

###### Parameter Matrix

In linear regression, the output or prediction is a single value $\hat{y}$, and I have parameter vector $\boldsymbol{\theta} = [\theta_0, \theta_1,\cdots ,\theta_d]$ remains the same for all predictions. Unlike that, in logistic regression, each class has its own parameters. And I have **parameter matrix** $\boldsymbol{\Theta}$.

$$\boldsymbol{\Theta} = \Big[ \boldsymbol{\theta}_1, \boldsymbol{\theta}_2, \boldsymbol{\theta}_3, \cdots, \boldsymbol{\theta}_K \Big]_{(d+1) \times K}$$ $$\boldsymbol{\theta}_k = \begin{bmatrix} \theta_0^k \\ \theta_{1}^k \\\theta_2^k \\ \vdots \\ \theta_{d}^k \end{bmatrix}_{(d+1) \times 1}$$

###### Model and Output

Logistic regression models the conditional probability, given the parameters $\boldsymbol{\Theta}$ to approximate this true conditional probability distribution.
$$P(y|\mathbf{x}; \boldsymbol{\Theta}) \approx P^*(y|\mathbf{x})$$
Just like linear regression, I have a linear combination of the weights and biases, that is, for a data point $\mathbf{x}$ and class $C_k$ with parameters $\boldsymbol{\theta}_k$, I define a $z_k$ as:
$$z_k = \theta_0^k + \theta_{1}^k x_{1}+ \theta_2^k x_{2} + \ldots + \theta_d^k x_{d}$$

Logistic regression **outputs a full probability vector** $\hat{\mathbf{y}}=[\hat{y}_1,\hat{y}_2, \cdots,\hat{y}_K]$ where each component $\hat{y}_k$ is given as a conditional probability:
$$\hat{y}_k = P(y=C_k \mid \mathbf{x};\boldsymbol{\Theta}) = \large \dfrac{e^{z_k}}{\sum_{j=1}^{K}e^{z_j}}$$Each $\hat{y}_k \in [0, 1]$ is the model’s predicted probability that the input belongs to class $C_k$, and the predicted label is the class with the largest $\hat{y}_k$ value, that is,
$$\text{Prediction} = \arg\max_k \hat{y}_k$$

### Symbols

Let $$\mathbf{x}=(x_1,\ldots,x_d)$$ be the feature vector and $$\boldsymbol{\theta}_k=(\theta_{k0},\theta_{k1},\ldots,\theta_{kd})$$ be the parameters for class $$C_k$$, where $$\theta_{k0}$$ is an intercept (bias) and $$\theta_{kj}$$ multiplies feature $$x_j$$ for $$j=1,\ldots,d$$. Stack these column-wise to form $$\boldsymbol{\Theta}=[\boldsymbol{\theta}_1,\ldots,\boldsymbol{\theta}_K]$$, a $$(d+1)\times K$$ parameter matrix if the bias is included as a separate term or via $$x_0=1$$

### Expanded form (no dot products)

For each class $$k$$, the logit (score) before softmax is $$z_k=\boldsymbol{\theta}_k^{\top}\tilde{\mathbf{x}}=\theta_{k0}+\sum_{j=1}^{d}\theta_{kj}x_j$$ where $$\tilde{\mathbf{x}}=(1,x_1,\ldots,x_d)$$ includes the bias 1 at the front. The corresponding probability is[8][2]
$$
\hat{y}_k
= \frac{\exp\!\left(\theta_{k0}+\theta_{k1}x_1+\theta_{k2}x_2+\cdots+\theta_{kd}x_d\right)}
{\sum_{j=1}^{K}\exp\!\left(\theta_{j0}+\theta_{j1}x_1+\theta_{j2}x_2+\cdots+\theta_{jd}x_d\right)} \,,
$$
so every $$\hat{y}_k$$ is an exponential of a linear combination of features, normalized by the sum of exponentials across all classes.[9][1]

### Concrete example (K=3)

With three classes and features $$(x_1,x_2,\ldots,x_d)$$,
$$
\hat{y}_1=\frac{e^{\theta_{10}+\theta_{11}x_1+\cdots+\theta_{1d}x_d}}
{e^{\theta_{10}+\theta_{11}x_1+\cdots+\theta_{1d}x_d}
+e^{\theta_{20}+\theta_{21}x_1+\cdots+\theta_{2d}x_d}
+e^{\theta_{30}+\theta_{31}x_1+\cdots+\theta_{3d}x_d}} \,,
$$
$$
\hat{y}_2=\frac{e^{\theta_{20}+\theta_{21}x_1+\cdots+\theta_{2d}x_d}}
{e^{\theta_{10}+\theta_{11}x_1+\cdots+\theta_{1d}x_d}
+e^{\theta_{20}+\theta_{21}x_1+\cdots+\theta_{2d}x_d}
+e^{\theta_{30}+\theta_{31}x_1+\cdots+\theta_{3d}x_d}} \,,
$$
$$
\hat{y}_3=\frac{e^{\theta_{30}+\theta_{31}x_1+\cdots+\theta_{3d}x_d}}
{e^{\theta_{10}+\theta_{11}x_1+\cdots+\theta_{1d}x_d}
+e^{\theta_{20}+\theta_{21}x_1+\cdots+\theta_{2d}x_d}
+e^{\theta_{30}+\theta_{31}x_1+\cdots+\theta_{3d}x_d}} \,,
$$
and $$\hat{y}_1+\hat{y}_2+\hat{y}_3=1$$ by construction.[1][8]

### What the predictions mean

The vector $$\hat{\mathbf{y}}$$ is a categorical distribution over classes conditioned on $$\mathbf{x}$$, so it gives class-membership probabilities rather than hard labels, enabling decisions like $$\arg\max_k \hat{y}_k$$ or thresholding for risk-sensitive choices. For $$K=2$$, this reduces to the sigmoid model $$P(y=1\mid \mathbf{x};\boldsymbol{\theta})=\sigma(\boldsymbol{\theta}^{\top}\mathbf{x})$$, making binary logistic regression a special case of the softmax formulation.[10][11][8][1]

[1](https://en.wikipedia.org/wiki/Softmax_function)
[2](http://deeplearning.stanford.edu/tutorial/supervised/SoftmaxRegression/)
[3](https://sebastianraschka.com/faq/docs/softmax_regression.html)
[4](https://stackoverflow.com/questions/36051506/difference-between-logistic-regression-and-softmax-regression)
[5](https://www.geeksforgeeks.org/python/softmax-regression-using-tensorflow/)
[6](https://rasbt.github.io/mlxtend/user_guide/classifier/SoftmaxRegression/)
[7](https://www.kdnuggets.com/2016/07/softmax-regression-related-logistic-regression.html)
[8](https://d2l.ai/chapter_linear-classification/softmax-regression.html)
[9](https://www.geeksforgeeks.org/machine-learning/understanding-logistic-regression/)
[10](https://www.cse.iitk.ac.in/users/nsrivast/CS771/lec14.pdf)
[11](https://en.wikipedia.org/wiki/Logistic_regression)

P(y=C_k|\mathbf{x}; \boldsymbol{\Theta}) = \frac{e^{\boldsymbol{\theta}_k^T \mathbf{x}}}{\sum_{j=1}^{K} e^{\boldsymbol{\theta}_j^T \mathbf{x}}}P(y=Ck∣x;Θ)=∑j=1KeθjTxeθkTx
where Θ={θ1,θ2,...,θK}\boldsymbol{\Theta} = {\boldsymbol{\theta}1, \boldsymbol{\theta}_2, ..., \boldsymbol{\theta}_K}Θ={θ1,θ2,...,θK} represents the parameter vectors for all KKK classes.wikipedia+2​

This formulation encompasses:

- Binary classification (K=2K=2K=2): Simplifies to the sigmoid function
    
- Multi-class classification (K>2K>2K>2): Uses the softmax function
    

Probabilistic Formulation

The model
Assuming all samples are independent and identically distributed, the likelihood function is:

L(Θ)=P(D∣Θ)=∏i=1nP(y(i)∣x(i);Θ)L(\boldsymbol{\Theta}) = P(\mathcal{D}|\boldsymbol{\Theta}) = \prod_{i=1}^{n} P(y^{(i)}|\mathbf{x}^{(i)}; \boldsymbol{\Theta})L(Θ)=P(D∣Θ)=∏i=1nP(y(i)∣x(i);Θ)

=∏i=1n∏k=1KP(y=Ck∣x(i);Θ)1(y(i)=Ck)= \prod_{i=1}^{n} \prod_{k=1}^{K} P(y=C_k|\mathbf{x}^{(i)}; \boldsymbol{\Theta})^{\mathbb{1}(y^{(i)}=C_k)}=∏i=1n∏k=1KP(y=Ck∣x(i);Θ)1(y(i)=Ck)

where 1(y(i)=Ck)\mathbb{1}(y^{(i)}=C_k)1(y(i)=Ck) is an indicator function that equals 1 when y(i)=Cky^{(i)}=C_ky(i)=Ck and 0 otherwise.sciencedirect​image.jpg​

Maximum Likelihood Estimation

Taking the log-likelihood (since log is monotonic):

ℓ(Θ)=log⁡L(Θ)=∑i=1n∑k=1K1(y(i)=Ck)log⁡P(y=Ck∣x(i);Θ)\ell(\boldsymbol{\Theta}) = \log L(\boldsymbol{\Theta}) = \sum_{i=1}^{n} \sum_{k=1}^{K} \mathbb{1}(y^{(i)}=C_k) \log P(y=C_k|\mathbf{x}^{(i)}; \boldsymbol{\Theta})ℓ(Θ)=logL(Θ)=∑i=1n∑k=1K1(y(i)=Ck)logP(y=Ck∣x(i);Θ)

We maximize this to find optimal parameters:

Θ∗=arg⁡max⁡Θℓ(Θ)=arg⁡max⁡Θ∑i=1nlog⁡P(y(i)∣x(i);Θ)\boldsymbol{\Theta}^* = \arg\max_{\boldsymbol{\Theta}} \ell(\boldsymbol{\Theta}) = \arg\max_{\boldsymbol{\Theta}} \sum_{i=1}^{n} \log P(y^{(i)}|\mathbf{x}^{(i)}; \boldsymbol{\Theta})Θ∗=argmaxΘ

Sigmoid Function



where Θ={θ1,θ2,...,θK}\boldsymbol{\Theta} = \{\boldsymbol{\theta}_1, \boldsymbol{\theta}_2, ..., \boldsymbol{\theta}_K\}Θ={θ1,θ2,...,θK} represents the parameter vectors for all KKK classes.[wikipedia+2](https://en.wikipedia.org/wiki/Multinomial_logistic_regression)​

This formulation encompasses:

- **Binary classification** (K=2K=2K=2): Simplifies to the sigmoid function
    
- **Multi-class classification** (K>2K>2K>2): Uses the softmax function
    

## Probabilistic Formulation

The model
Assuming all samples are independent and identically distributed, the **likelihood function** is:

L(Θ)=P(D∣Θ)=∏i=1nP(y(i)∣x(i);Θ)L(\boldsymbol{\Theta}) = P(\mathcal{D}|\boldsymbol{\Theta}) = \prod_{i=1}^{n} P(y^{(i)}|\mathbf{x}^{(i)}; \boldsymbol{\Theta})L(Θ)=P(D∣Θ)=∏i=1nP(y(i)∣x(i);Θ)

=∏i=1n∏k=1KP(y=Ck∣x(i);Θ)1(y(i)=Ck)= \prod_{i=1}^{n} \prod_{k=1}^{K} P(y=C_k|\mathbf{x}^{(i)}; \boldsymbol{\Theta})^{\mathbb{1}(y^{(i)}=C_k)}=∏i=1n∏k=1KP(y=Ck∣x(i);Θ)1(y(i)=Ck)

where 1(y(i)=Ck)\mathbb{1}(y^{(i)}=C_k)1(y(i)=Ck) is an indicator function that equals 1 when y(i)=Cky^{(i)}=C_ky(i)=Ck and 0 otherwise.[sciencedirect](https://www.sciencedirect.com/topics/mathematics/multinomial-logistic-regression)​image.jpg​

## Maximum Likelihood Estimation

Taking the log-likelihood (since log is monotonic):

ℓ(Θ)=log⁡L(Θ)=∑i=1n∑k=1K1(y(i)=Ck)log⁡P(y=Ck∣x(i);Θ)\ell(\boldsymbol{\Theta}) = \log L(\boldsymbol{\Theta}) = \sum_{i=1}^{n} \sum_{k=1}^{K} \mathbb{1}(y^{(i)}=C_k) \log P(y=C_k|\mathbf{x}^{(i)}; \boldsymbol{\Theta})ℓ(Θ)=logL(Θ)=∑i=1n∑k=1K1(y(i)=Ck)logP(y=Ck∣x(i);Θ)

We maximize this to find optimal parameters:

Θ∗=arg⁡max⁡Θℓ(Θ)=arg⁡max⁡Θ∑i=1nlog⁡P(y(i)∣x(i);Θ)\boldsymbol{\Theta}^* = \arg\max_{\boldsymbol{\Theta}} \ell(\boldsymbol{\Theta}) = \arg\max_{\boldsymbol{\Theta}} \sum_{i=1}^{n} \log P(y^{(i)}|\mathbf{x}^{(i)}; \boldsymbol{\Theta})Θ∗=argmaxΘ

#### Sigmoid Function

>[!cite] Sigmoid Function
>A sigmoid function is any function whose graph has an S‑shaped (called the **sigmoid curve**). Its a **bounded, differentiable, real** function that is defined for all real input values and has a positive derivative at each point.
>
>The **standard logistic function**, $\sigma(x): \mathbb{R} \rightarrow [0,1]$, is a specific type of sigmoid function, of the form:
>$$\displaystyle \sigma (x)={\frac {1}{1+e^{-x}}}={\frac {e^{x}}{1+e^{x}}}$$
>- It maps any real value to a value between $0$ and $1$
>- $\lim_{x \rightarrow \infty} = 1$ and  $\lim_{x \rightarrow -\infty} = 0$
>- $\sigma(x) + \sigma(-x) = 1$
>
>![[logistic-function-curve.png|#invert_B|400]]
>*Mostly for ML, the terms sigmoid and logistic function are used inter-changeably.*
^def-sigmooid-function


