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

# Epanechnikov Kernel Function

Of course. The statement that the Epanechnikov kernel is "optimal" is a specific, theoretical claim about its mathematical properties. To understand it, we need to break down what "Asymptotic Mean Integrated Squared Error (AMISE)" means and why minimizing it is the goal.

## 1. How is the "Error" of a Density Estimate Measured?

First, we need a way to quantify how "wrong" our kernel density estimate is. The goal is to estimate the true, unknown probability density function, which we'll call `f(x)`. Our estimate, created using a kernel and a bandwidth `h`, is called `f̂_h(x)`.

- **Integrated Squared Error (ISE):** The most common way to measure the total error across all possible values of `x` is the Integrated Squared Error. It's the integral of the squared difference between the true function and our estimate.[wikipedia](https://en.wikipedia.org/wiki/Kernel_density_estimation)​
    ISE=∫(f^h(x)−f(x))2dx\text{ISE} = \int ( \hat{f}_h(x) - f(x) )^2 dxISE=∫(f^h(x)−f(x))2dx
    This value tells you how much your _specific_ estimate (based on your one sample of data) deviates from the truth.
    
- **Mean Integrated Squared Error (MISE):** The ISE depends on the particular random sample you happened to draw. A better measure of an estimator's quality is the _Mean_ Integrated Squared Error, which is the expected (or average) ISE over all possible random samples.[wikipedia](https://en.wikipedia.org/wiki/Kernel_density_estimation)​
    MISE=E[ISE]=E[∫(f^h(x)−f(x))2dx]\text{MISE} = E[\text{ISE}] = E\left[\int ( \hat{f}_h(x) - f(x) )^2 dx\right]MISE=E[ISE]=E[∫(f^h(x)−f(x))2dx]
    **MISE is the gold standard for evaluating the performance of a density estimator.** A lower MISE is better.
    

## 2. What is "Asymptotic" MISE (AMISE)?

The exact formula for MISE is very complex and depends on the unknown true density `f(x)`. However, for large sample sizes (`n`), the MISE can be accurately approximated by a simpler formula. This approximation is the **Asymptotic Mean Integrated Squared Error (AMISE)**.[stat.berkeley+2](https://www.stat.berkeley.edu/~stark/Teach/S240/Notes/ch10.pdf)​

The AMISE formula has two main parts, which perfectly illustrate the bias-variance tradeoff:
AMISE(h)=R(K)nh⏟Variance Component+14h4(∫x2K(x)dx)2R(f′′)⏟Squared Bias Component\text{AMISE}(h) = \underbrace{\frac{R(K)}{nh}}_{\text{Variance Component}} + \underbrace{\frac{1}{4}h^4 \left( \int x^2 K(x) dx \right)^2 R(f'')}_{\text{Squared Bias Component}}AMISE(h)=Variance ComponentnhR(K)+Squared Bias Component41h4(∫x2K(x)dx)2R(f′′)

Let's break down these two components:

1. **Variance Component:** This part of the error comes from the randomness in the data sample. It decreases as sample size `n` and bandwidth `h` increase. The term `R(K)` is the integral of the squared kernel function, sometimes called its "roughness." A "spikier" kernel has a higher `R(K)`.[wikipedia](https://en.wikipedia.org/wiki/Kernel_density_estimation)​
    
2. **Squared Bias Component:** This part of the error comes from the estimator systematically differing from the true function (e.g., smoothing over sharp peaks). It is primarily driven by the bandwidth `h` (a larger `h` increases bias) and the shape of the kernel, captured by the term `∫x²K(x)dx`, which is the variance of the kernel itself.[wikipedia](https://en.wikipedia.org/wiki/Kernel_density_estimation)​
    

## 3. Why the Epanechnikov Kernel is "Optimal"

The goal is to choose a kernel function `K` that makes the AMISE as small as possible. Looking at the AMISE formula, the parts related to the kernel function are `R(K)` and `∫x²K(x)dx`.

The optimization problem is to find a kernel function `K` that minimizes the AMISE. It has been mathematically proven that among all possible symmetric kernel functions, the one that minimizes the kernel-dependent parts of the AMISE formula is the **Epanechnikov kernel**.[towardsdatascience+2](https://towardsdatascience.com/the-math-behind-kernel-density-estimation-5deca75cba38/)​

- Its parabolic shape provides the most efficient tradeoff between reducing the kernel's "roughness" (`R(K)`) and its variance (`∫x²K(x)dx`).
    
- In short, for any given bandwidth, the Epanechnikov kernel produces the lowest possible asymptotic error, making it theoretically the most efficient kernel for density estimation.[engr.oregonstate+1](https://web.engr.oregonstate.edu/~fuxia/aaai18cameraready.pdf)​
    

## Practical Implications

While the Epanechnikov kernel is theoretically optimal, the practical difference between it and other common kernels like the Gaussian kernel is very small. The efficiency of the Gaussian kernel is about 95% that of the Epanechnikov kernel. In practice, the choice of **bandwidth `h`** has a much more significant impact on the final density estimate than the choice of the kernel function.[hydrology.usu](https://hydrology.usu.edu/dtarb/Rajagopalan_Lall_Tarboton_1997.pdf)​

1. [https://towardsdatascience.com/the-math-behind-kernel-density-estimation-5deca75cba38/](https://towardsdatascience.com/the-math-behind-kernel-density-estimation-5deca75cba38/)
2. [https://www.sciencedirect.com/topics/mathematics/epanechnikov-kernel](https://www.sciencedirect.com/topics/mathematics/epanechnikov-kernel)
3. [https://web.engr.oregonstate.edu/~fuxia/aaai18cameraready.pdf](https://web.engr.oregonstate.edu/~fuxia/aaai18cameraready.pdf)
4. [https://en.wikipedia.org/wiki/Kernel_density_estimation](https://en.wikipedia.org/wiki/Kernel_density_estimation)
5. [https://www.stat.berkeley.edu/~stark/Teach/S240/Notes/ch10.pdf](https://www.stat.berkeley.edu/~stark/Teach/S240/Notes/ch10.pdf)
6. [https://www.thaiscience.info/journals/Article/CMJS/10905433.pdf](https://www.thaiscience.info/journals/Article/CMJS/10905433.pdf)
7. [https://hydrology.usu.edu/dtarb/Rajagopalan_Lall_Tarboton_1997.pdf](https://hydrology.usu.edu/dtarb/Rajagopalan_Lall_Tarboton_1997.pdf)
8. [https://faculty.washington.edu/yenchic/17Sp_403/Lec7-density.pdf](https://faculty.washington.edu/yenchic/17Sp_403/Lec7-density.pdf)