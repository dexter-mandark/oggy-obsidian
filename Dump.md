>$$f(v) = \frac{1}{n} \times \sum_{i=1}^{n}(x_{i} − v)^{2} $$

# Basic Definitions of Probability

#### Classical Definition

The probability of an event is the ratio of the number of favorable outcomes to the total number of equally likely outcomes.
- **Example:** The probability of rolling a 3 on a fair six-sided die is 1/6, because there is one favorable outcome (rolling a 3) and six total equally likely outcomes.
- **Limitation:** This only works when all outcomes are equally likely, which is not always the case.

#### Subjective (Bayesian) Probability

This defines probability as a measure of an individual's degree of belief that an event will occur. This is particularly useful for single, unrepeatable events.
- **Example:** "There is a 20% chance that RCB will win this year". I cannot repeat this year's IPL thousands of times, so this probability is based on personal belief, informed by available evidence.

# Expected Value

>[!quote] Measure-Theoretic definition of mean
>Mean is the expected value of a measurable function with respect to a probability measure.
>Given a measure space $(\Omega, \mathcal{F}, \mu)$ and where:
>- $\Omega$ is the sample space
>- $\mathcal{F}$ is a $\sigma$-algebra of measurable sets
>- $\mu$ is a measure function $\mu: \mathcal{F} \rightarrow [0, \infty]$ on $(\Omega, \mathcal{F})$.
>

For a measurable function $X: \Omega \to \mathbb{R}$, the mean or the expected value $\mathbb{E}[X]$, is defined as: $$\mathbb{E}[X] = \int_{\Omega} X(\omega) \, d\mu(\omega)$$

 when this integral exists.

