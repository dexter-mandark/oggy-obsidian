---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Frequency Distribution

A **frequency distribution** is a summary that shows how many times each different value (or group of values) appears in a dataset.

There are two primary ways to present a frequency distribution:
- **Frequency Tables:** This is a simple table that lists the possible outcomes and their corresponding frequencies.​
- **Graphs:** A common way to visualize a frequency distribution is with a chart, a *bar chart* or a *histogram*.

#### Frequency Distribution as an Empirical Measure

A frequency distribution is an empirical approximation of an underlying probability distribution.

Let $(\Sigma,\mathcal{F},P)$ be a [[Probability#The Probability Space $( Omega, mathcal{F}, P)$|Probability Space]]. Let $X:\Sigma \rightarrow \mathbb{R}$ be a real-valued Random Variable.
Given a set of $n$ *independent and identically distributed* observations $\{{x_{1},x_{2},…,x_{n}}\}$ drawn from the distribution of $X$, I can define the **empirical measure (or empirical distribution)**, $P_{n}$ for any measurable set $A \in \mathcal{F}$ as:

$$P_{n}(A)=\frac{1}{n}\sum_{i=1}^{n} \mathbb{I}_{A}(x_{i})$$

Here, $\mathbb{I}_{A}(x_{i})$ is the [[Definitions#^def-indicator-function|indicator function]]. And the function $P_{n}(A)$, gives the proportion of observations that fall into the set $A$. The collection of these proportions for all measurable sets constitutes the empirical frequency distribution.

>[!tip] Glivenko-Cantelli theorem
>The Glivenko-Cantelli theorem states that as the number of observation, $n \rightarrow \infty$ the empirical distribution function converges uniformly to the true cumulative distribution function (CDF) of the random variable.

## Histograms

A histogram is a graphical representation of the distribution of numerical data.

![[histogram.png|#invert_B|400]]

It visualizes a frequency distribution of a dataset $S$.
- The horizontal axis represents the range of values in the dataset, from the minimum ($x_\text{min}$) to the maximum ($x_\text{max}$).
- This range is divided into a number of intervals, or **bins** ($m$).
- The vertical bars represent the **frequency** or count of data points from the set $S$ that fall within each specific bin.

#### Building the histogram

1. I have $m$ bins: $\{b_{0},b_{1},b_{2}, \cdots,b_{m}\}$
2. And a histogram array $H$, where $H[j]$ represents the number of data points in bin $b_{j}$.

3. Assuming, each bin is of equal width, $$b_{w} = \frac{x_{\text{max}} - x_{\text{min}}}{m}$$

4. Each data point $x_{i}$ is assigned to a bin $b_{j}$ using the formula: $$b_{j}= \left\lfloor \frac{x_i - x_{\min}}{b_w} \right\rfloor$$

5. Finally the complete thing can be expressed as:  $$H[j] = \sum_{i=1}^{n} \delta \left( \left\lfloor \frac{x_i - x_{\min}}{b_{w}} \right\rfloor - j \right)$$

6. Where $\delta$ is the [[Definitions#^def-krondelta-function|Kronecker Delta Function]].

>[!warning] Zero Indexed
>Here the array $H[j]$ is zero indexed, if it were 1-indexed, I would just add one to the $b_{j}$ value.

#### Normalized Histogram

Rather than having an **absolute frequency histogram**, where $H[j]$ is the raw count of how many data points fall into each bin $b_{j}$. I can have a **normalized histogram**.

###### Normalization to a Probability Distribution (Relative Frequency)

- Each bin's raw count is divided by the total number of data points.
- The height of each bar represents the **proportion or percentage** of the total data that falls into that bin .
- The sum of the heights of all the bars in the histogram **equals 1** (or 100%).​
- Why ?
	- Allows me to *interpret the histogram as a discrete probability distribution*, showing how likely it is for a random observation to fall into a specific bin.
	- It is also essential for comparing two datasets that have different sample sizes

$$H[j] =\frac{1}{n} \sum_{i=1}^{n} \delta \left( \left\lfloor \frac{x_i - x_{\min}}{b_{w}} \right\rfloor - j \right)$$

###### Normalization to a Probability Density Function (PDF)

- Each bin's count is divided by the total number of data points and by the width of the bin.
- The *area of each bar represents the probability of the data* falling within that bin's range.
- The total **area under the entire histogram equals 1**.
- Why ?
    - Its crucial when using a *histogram to estimate the underlying probability density function* of a continuous variable.
    - It makes the histogram's shape independent of the chosen bin width, which is important for more advanced statistical modeling.
