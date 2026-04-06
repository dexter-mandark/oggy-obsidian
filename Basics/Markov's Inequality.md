---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Markov's Inequality

Markov's inequality is a fundamental result in probability theory that provides an upper bound on the probability that a non-negative [[Random Variable#^def-random-variable|random variable]] exceeds a certain threshold. *It gives us an upper limit on how likely it is for a non-negative random variable to be "surprisingly large".*

>[!cite] Markov's Inequality
>It states that for any non-negative random variable $X$ and any positive constant $a >0$, the probability that $X$ is at least $a$ satisfies:
>$$P(X \geq a) \leq \frac{E[X]}{a}$$
>where $E[X]$ is the expected value (mean) of $X$
^def-markov-inequality

The inequality is remarkably simple yet powerful because it requires minimal information -
- only that the random variable is non-negative
- knowledge of its expected value.
**It makes no assumptions about the specific distribution of the random variable**, meaning the bound must hold for all possible distributions.

###### Why it makes sense

The core insight that makes this always true: *If something has a small average, it can't be large very often.*

Suppose the average income in a population is $\$40,000$. What's the maximum probability that a randomly selected person earns more than $\$200,000$ ?
Using Markov's Inequality, I can write the problem as:
$$P(X \geq 200,000) = \frac{E[X]}{200,000} = \frac{40,000}{200,000} =0.2$$
If more than $20\%$ of people earned over $\$200,000$, then even if everyone else earned $\$0$, the average would exceed $\$40,000$. Since, I know the average is exactly $\$40,000$, it's mathematically impossible for more than $20\%$ to earn that much.

###### Proof

Let $X$ be a continuous random variable with probability density function $f(x)$. By definition, the expected value $E[X]$ is the integral of $x$ weighted by its probability density. Since XX is non-negative, I can define $E[X]$ as:
$$E[X] = \int_{0}^{\infty} x f(x)\,dx$$
And for a non-zero value $a$, I can divide the range into two parts $[0, a)$ and $[a, \infty)$:
$$E[X] = \int_{0}^{a} x f(x)\,dx + \int_{a}^{\infty} x f(x)\,dx$$
Since $x\geq0$ and $f(x)\geq0$, the first term $\int_{0}^a x.f(x) \ dx$ is always greater than or equal to $0$. I can drop that and the remaining value is less than or equal to the original expectation.
$$E[X] \ge \int_{a}^{\infty} x f(x)\,dx$$
In the remaining integral range $[a,\infty)$ the value of $x$ is always greater than or equal to $a$ (i.e., $x \geq a$). I can substitute $a$ for $x$ to create a smaller or equal lower bound and then substitute the definition of probability of $P(X \ge a)$.
$$\begin{align} \int_{a}^{\infty} x f(x)\,dx & \ge \int_{a}^{\infty} a f(x)\,dx \\ \\ & \geq a\int_{a}^{\infty} f(x)\,dx \\ \\ & \ge a\ P(X \ge a) \end{align}$$
Finally, I can substitute this result into the original expectation to get:
$$\begin{align} E[X] & \ge a\,{P}(X \ge a) \\ \\ \frac{E[X]}{a} & \ge \,{P}(X \ge a)\end{align}$$

## Chebeshey's Inequality