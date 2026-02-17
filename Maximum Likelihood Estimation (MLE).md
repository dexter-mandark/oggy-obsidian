---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

In [[Estimation Theory]] my focus was on _analyzing_ the properties of estimators (bias/variance). Someone handed me a formula (like $\frac{1}{n}\sum x_i$) and asked, "Is this good? Is it biased? Is the variance low?".

In Maximum Likelihood Estimation my focus is on *inventing* these estimators. I'm starting with nothing but data and a model assumption (e.g., "this is a Gaussian"). And I have to invent the best formula to find the parameters. I ask, "How do I build a tool that extracts $\mu$ and $\sigma$ from this raw data?"

## The Intuition

I have a observed single data point $x_1$. And I have two competing hypotheses about which population this data came from (two candidate Gaussian distributions)
- Model-1: $\mathcal{G}_1(\mu_1, \sigma_1)$
- Model-2: $\mathcal{G}_2(\mu_2, \sigma_2)$.
Now given $x_1$ is observed, *which model is more believable? Which model is the likely one?*

For a Gaussian distribution, the probability density function (PDF) is:
$$ P(x|\mu, \sigma) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp \left( -\frac{(x-\mu)^2}{2\sigma^2} \right) $$

I can simply calculate the probability (density) of seeing $x_1$ under both models.
If $P(x_1 | \mu_2, \sigma_2) > P(x_1 | \mu_1, \sigma_1)$, I say:  "$\mathcal{G}_2(\mu_2, \sigma_2)$ is **more likely** given the data".
The "height" of each curve at $x_1$ represents how "compatible" that model is with the observation, it tells me **how likely (likelihood)**.
*This does **not** mean Model 2 is definitely correct, it just means the data is more consistent with Model 2's assumptions.*

#### Likelihood vs. Probability

- **Probability:** Data is the random variable; parameters are fixed.
    - "What is the probability of seeing $x=6.5$ if the true mean is $\mu=5$?"
    - $P(\text{data} | \theta)$
- **Likelihood:** Parameters are the variable; data is fixed (observed).
    - "Given that I observed $x=6.5$, how 'likely' is it that $\mu=5$?"
    - $\mathcal{L}(\theta | \text{data})$

The interpretation flips. In MLE, I treat the observed data as fixed facts and ask: _"Which parameter values make this data most plausible?"_

## Model Selection with Two I.I.D. Observations

I observe two measurements: $\mathbf{x} = \{x_1, x_2\}$. With the assumption that they are [[Estimation Theory#Independent and Identically Distributed Data|Independently and Identically distributed]]:
- Both observations are drawn from the same underlying probability distribution with unknown parameters $\theta$. $$ x_1, x_2 \sim P(x \mid \theta) $$
- The outcome of $x_1$ does not influence the outcome of $x_2$. Formally:
$$ P(x_1, x_2 \mid \theta) = P(x_1 \mid \theta) \cdot P(x_2 \mid \theta) $$

Now I propose two competing hypotheses:
- $x_1, x_2 \sim \mathcal{G}_1(\mu_1, \sigma_1)$ where $\theta_1 = (\mu_1, \sigma_1)$ are fixed parameters defining Model 1.
- $x_1, x_2 \sim \mathcal{N}(\mu_2, \sigma_2^2)$ where $\theta_2 = (\mu_2, \sigma_2^2)$ are fixed parameters defining Model 2.

Simple question: Which one of these hypotheses is correct ?
*Formally, given $x_1, x_2$ what is the probability of observing $\theta_1$. What is the likelihood of $\theta_1$? And what is the likelihood of $\theta_2$? Which ever has the maximum likelihood is my answer.*

For both the models I can define the **likelihoods** $\mathcal{L_1}$ and $\mathcal{L}_2$.
$$\begin{align}  \mathcal{L_1}(\theta_1 \mid x_1, x_2) &= P(x_1, x_2 \mid \mu_1, \sigma_1) \\ \\  \mathcal{L_2}(\theta_2 \mid x_1, x_2) &= P(x_1, x_2 \mid \mu_2, \sigma_2)  \end{align}$$

By the I.I.D. assumption:
$$\mathcal{L_1}(\theta_1 \mid x_1, x_2) = P(x_1 \mid \mu_1, \sigma_1) \times P(x_2 \mid \mu_1, \sigma_1) $$

Substituting the Gaussian PDF:
$$\begin{align}  &= \frac{1}{\sqrt{2\pi\sigma_1^2}} \exp\left( -\frac{(x_1-\mu_1)^2}{2\sigma_1^2} \right) \times \frac{1}{\sqrt{2\pi\sigma_1^2}} \exp\left( -\frac{(x_2-\mu_1)^2}{2\sigma_1^2} \right) \\ \\  &= \frac{1}{2\pi\sigma_1^2} \exp\left( -\frac{(x_1-\mu_1)^2 + (x_2-\mu_1)^2}{2\sigma_1^2} \right) \end{align}$$

Similarly, for Model-2
$$ \mathcal{L}_2(\theta_2 \mid x_1, x_2) = \frac{1}{2\pi\sigma_2^2} \exp\left( -\frac{(x_1-\mu_2)^2 + (x_2-\mu_2)^2}{2\sigma_2^2} \right) $$

I choose the model with the higher likelihood:
- If $\mathcal{L}(\theta_1 \mid x_1, x_2) > \mathcal{L}(\theta_2 \mid x_1, x_2)$, select Model 1.
- If $\mathcal{L}(\theta_2 \mid x_1, x_2) > \mathcal{L}(\theta_1 \mid x_1, x_2)$, select Model 2.

## MLE for a Gaussian Distribution

Given a dataset $\mathcal{D} = \{x_1, x_2, \dots, x_n\}$. With the assumption that the data is drawn **IID** from a Gaussian Distribution with unknown parameters $\theta = (\mu, \sigma^2)$:
$$ x_i \sim \mathcal{G}(\mu, \sigma^2) $$

I want to find the "**most likely**" values of $\mu_{mle}$ and $\sigma^2_{mle}$ that generated this observed data.

###### The Likelihood Function

Since the data is i.i.d., the joint probability of observing all $n$ data points is the product of individual probabilities:
$$ P(\mathcal{D} \mid \theta) = \prod_{i=1}^n P(x_i \mid \theta) $$

Substituting $v = \sigma^2$ and the Gaussian Probability density function:
$$ P(x \mid \mu, v) = \frac{1}{\sqrt{2\pi v}} \exp\left({-\frac{(x-\mu)^2}{2v}}\right) $$
The likelihood function would be:
$$\begin{align}  \mathcal{L}(\mu, v \mid \mathcal{D}) &= \prod_{i=1}^n P(x_i \mid \theta)\\ \\  &= \prod_{i=1}^n P(x_i \mid \mu, v) \\ \\ \mathcal{L}(\mu, v) &= \prod_{i=1}^n \frac{1}{\sqrt{2\pi v}} \exp\left( -\frac{(x_i-\mu)^2}{2v} \right)  \end{align}$$

###### Log-Likelihood

I don't want to deal with products, so instead of maximizing $\mathcal{L}(\mu, v)$, I maximize $\ln \mathcal{L}(\mu, v)$.
$$\begin{align}  \ln \mathcal{L}(\mu, v) &= \ln \prod_{i=1}^n P(x_i \mid \mu, v) = \sum_{i=1}^n \ln P(x_i \mid \mu, v) \\ \\ \ln \mathcal{L}(\mu, v) &= \sum_{i=1}^n \left[ \ln\left(\frac{1}{\sqrt{2\pi v}}\right) - \frac{(x_i-\mu)^2}{2v} \right] \\ \\  &= \sum_{i=1}^n \left[ -\frac{1}{2}\ln(2\pi v) - \frac{(x_i-\mu)^2}{2v} \right] \\ \\  &= -\frac{n}{2}\ln(2\pi) - \frac{n}{2}\ln v - \frac{1}{2v}\sum_{i=1}^n (x_i-\mu)^2  \end{align}$$

>[!warning] Why taking the log works ?
>Taking the **natural logarithm** to convert the product into a sum makes the math easier. The logarithm is a **strictly monotonically increasing function**, which means it preserves the location of the maximum.
>
>If $f(x)$ is a strictly increasing function and $g(x)$ reaches its maximum at $x^*$, then $f(g(x))$ also reaches its maximum at the same point $x^*$.
>
>For Maximum Likelihood estimation:
> - Let $\theta^*$ be the value that maximizes $L(\theta)$
>     
> - Since $\ln(x)$ is strictly increasing for all $x > 0$:
>     $$L(\theta_1)>L(\theta_2) \Longleftrightarrow  \ln⁡(L(\theta_1))>\ln⁡(L(\theta_2))$$
>     
> - Therefore, the value $\theta^*$ that maximizes $L(\theta)$ is **identical** to the value that maximizes $\ln(L(\theta))$
> 

###### Finding MLE for Mean ($\mu$)

To maximize $\mathcal{L}(\mu, v)$, take the partial derivative with respect to $\mu$ and set it to zero:

$$ \frac{\partial \ln \mathcal{L}(\mu, v)}{\partial \mu} = 0 $$

Computing the derivative, (only the last term depends on $\mu$):
$$\begin{align} \frac{\partial \ln \mathcal{L}(\mu, v)}{\partial \mu} &=  \frac{\partial}{\partial \mu}\left[ -\frac{1}{2v}\sum_{i=1}^n (x_i-\mu)^2 \right]\\ \\  &= -\frac{1}{2v} \sum_{i=1}^n\left[ \frac{\partial (x_i-\mu)^2}{\partial \mu}  \right]\\ \\  &= -\frac{1}{2v} \sum_{i=1}^n 2(x_i-\mu)(-1) \\ \\ &= \frac{1}{v} \sum_{i=1}^n (x_i-\mu)  \end{align}$$
Setting equal to zero and getting rid of $v$ as $v >0$:
$$\begin{align}  &\frac{1}{v} \sum_{i=1}^n (x_i-\mu) = 0 \\ \\ &  \sum_{i=1}^n x_i - n\mu = 0 \\ \\ & n\mu = \sum_{i=1}^n x_i \\ \\ \end{align}$$
$$ \large\boxed{\mu_{mle} = \frac{1}{n}\sum_{i=1}^n x_i} $$
**The Maximum Likelihood Estimation for the mean is simply the sample mean.**

>[!tip] The fuck does this mean ?
>**I ask:** "Given that I observed these specific data points $\mathcal{D}\{x_{1},x_{2},…,x_{n}\}$, which value of $\mu$ makes it most likely that these points came from $\mathcal{N}(\mu, \sigma^2)$?"
>**Answer:** Among all possible values $\mu$ could take ($\mu \in \mathbb{R}$), the value that *maximizes the probability* of having observed exactly this dataset is the sample mean $\bar{x}$.
>
>**Given:** A dataset $\mathcal{D} = {x_1, x_2, \dots, x_n}$ where each $x_i$ is drawn independently from the same Gaussian distribution $\mathcal{N}(\mu, \sigma^2)$ with unknown mean $\mu$ and variance $\sigma^2$.
>**Result:** The value of $\mu$ that maximizes the likelihood function $\mathcal{L}(\mu, \sigma^2 | \mathcal{D})$, that is, the parameter value that makes the observed data most probable - is the sample mean $\bar{x} = \mu_{mle} = \frac{1}{n}\sum_{i=1}^n x_i$.
>
>*Among all possible Gaussian distributions with different means, the one centered at the sample mean is the "best fit" for the data in the maximum likelihood sense.*

###### Finding MLE for Variance ($v = \sigma^2$)

Take the partial derivative with respect to $v$:
$$ \frac{\partial \ln \mathcal{L}(\mu, v)}{\partial v} = 0 $$
Computing the derivative:
$$ \frac{\partial \ln \mathcal{L}(\mu, v)}{\partial v} = -\frac{n}{2v} - \frac{1}{2}\left(-\frac{1}{v^2}\right)\sum_{i=1}^n (x_i-\mu)^2 $$
$$ = -\frac{n}{2v} + \frac{1}{2v^2}\sum_{i=1}^n (x_i-\mu)^2 $$

Setting equal to zero, and getting rid of $2v^2$ (which I can cause $v >0$ ):
$$\begin{align}  & -\frac{n}{2v} + \frac{1}{2v^2}\sum_{i=1}^n (x_i-\mu)^2 = 0 \\ \\  & -nv + \sum_{i=1}^n (x_i-\mu)^2 = 0  \end{align}$$
Solving for $v_{mle}$ :
$$ v_{mle} = \frac{1}{n}\sum_{i=1}^n (x_i-\mu)^2 $$

Since $\mu$ is unknown, I substitute my best estimate of $\mu$, i.e. $\mu_{mle}$ :
$$ \large \boxed{v_{mle} = \sigma^2_{mle} = \frac{1}{n}\sum_{i=1}^n (x_i-\mu_{mle})^2} $$^def-sigma-mle

The MLE for variance is the **biased (naive) sample variance**.

>[!tip] The fuck does this mean ?
>**Given:** A dataset $\mathcal{D} = {x_1, x_2, \dots, x_n}$ where each $x_i$ is drawn independently from the same Gaussian distribution $\mathcal{N}(\mu, \sigma^2)$ with unknown mean $\mu$ and variance $\sigma^2$.
>**Result:** The value of $\sigma^2$ that maximizes the likelihood function $\mathcal{L}(\mu, \sigma^2 | \mathcal{D})$, that is, the parameter value that makes the observed data most probable is:  $\sigma^2_{mle} = \frac{1}{n}\sum_{i=1}^n (x_i-\mu_{mle})^2$
>
>*Among all possible Gaussian distributions with different variances, the one with variance equal to $\sigma^2_{mle}$ provides the "best fit" for the data in the maximum likelihood sense.*
>
>However, this [[Estimation Theory#Proof 1 Bias of the Naive Variance Estimator|estimator is biased]] and systematically **underestimates** the true variance, if I repeatedly draw samples and calculate $\sigma^2_{mle}$, the average of these estimates will be less than the true $\sigma^2$ by a factor of $\frac{n-1}{n}$.

>[!danger]
>Important thing to note here is: **"Most Likely" $\neq$ "Unbiased"**.
>MLE optimizes for fitting the data, not for statistical unbiasedness.
>
>In fact, the [[Maximum Likelihood Estimation (MLE)#^def-sigma-mle|substitution]] of $\mu = \mu_{mle}$ in $v_{mle}$ is precisely what causes the bias! When I use $\mu_{mle}$ (which is calculated from the same data), I'm measuring deviations from the sample mean rather than the true mean. The sample mean is positioned to minimize distances to the data points, so the calculated variance is artificially reduced by $\frac{\sigma^2}{n}$.
>
>If I magically knew the true $\mu$ and used it directly, then $\frac{1}{n}\sum(x_i - \mu_{true})^2$ would actually be unbiased!


