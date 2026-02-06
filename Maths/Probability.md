---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Sample Spaces and Events

Every probabilistic model involves an underlying process, called the **experiment**, that will produce exactly one out of several possible **outcomes**.

![Probability#invert_B](expierment-sampleSpace-probabilty.png)

>[!cite] Sample Space
>The **set of all possible outcomes** is called the sample space of the experiment, and is denoted by $\Omega$.
>
>A sample space is a set of mutually exclusive and collectively exhaustive outcomes.
>- **Mutually Exclusive:** When the experiment is carried out, there is a unique outcome. The sample space associated with the roll of a die cannot contain “1 and 3” as a possible outcome.
>- **Collectively Exhaustive:** No matter what happens in the experiment, I should always obtain an outcome that has been included in the sample space.
>
>Sample Space can be a finite set, countable infinite or uncountable infinite set, depending upon the experiment.

There is no restriction on what constitutes an experiment. For example,
- Finite sample space: It could be a single toss of a coin, or three tosses
- Countable Infinite sample space: An inﬁnite sequence of tosses.
- Uncountable infinite sample space: Throwing a dart on a square target and viewing the point of impact as the outcome.

>[!cite] Event
>Any **subset of the sample space**, that is, a collection of possible outcomes, is called an event.
>
>If the outcome of an experiment is contained in event $E$, then I say *event $E$ has occurred*.

For two events $E$ and $F$,
- Event $E \cup F$ will occur if either $E$ or $F$ occurs.
- Event $E \cap F$ will occur if both $E$ and $F$ occur.
- Events $E$ and $F$ are said to be **mutually exclusive** if and only if $E \cap F = \phi$

## Probability as Relative Frequency

One way of deﬁning the probability of an event is in terms of its relative frequency.

>[!cite] Probability as relative frequency
>An experiment, with sample space $\Omega$, when is repeatedly performed, for each event $E$ of the sample space $\Omega$,  I deﬁne $n(E)$ to be the number of times event $E$ occurs, in the ﬁrst $n$ repetitions of the experiment.
>
>Then $P(E)$, the probability of the event $E$, is deﬁned as the (limiting) proportion of time that $E$ occurs. It is thus the **limiting frequency of $E$**.
>
>$$P(E) = \large{\lim_{n \rightarrow \infty}} \frac{n(E)}{n}$$

#### Problems with relative frequency based definition of probability

###### The Problem of Assignable Probabilities

An event can be any collection of possible outcomes, however, some sets have to be excluded.
In particular, when dealing with probabilistic models involving an uncountable inﬁnite sample space, there are certain unusual subsets for which one cannot associate meaningful probabilities.

*It's not possible to assign a meaningful probability to every conceivable subset of outcomes.*

###### The Problem of Convergence

The definition assumes that $\frac{n(E)}{n}$, the proportion of times an event occurs will settle down to a specific number as you repeat the experiment infinitely.
However, there's no logical guarantee within the definition itself that this limit exists or that it would be the same for each possible sequence of repetitions of the experiment ?

For example, suppose that the experiment to be repeatedly performed consists of ﬂipping a coin.
- How do I know that the proportion of heads obtained in the ﬁrst $n$ ﬂips *will converge to some value* as $n$ gets large?
- Also, even if it does converge to some value, how do I know that, if the experiment is repeatedly performed a second time, I shall obtain *the same limiting proportion* of heads?

>[!failure]
>This is a circular argument—it defines probability using a limit but can't prove the limit exists without using concepts of probability.

## Axiomatic Definition of Probability

The Russian mathematician *Andrey Kolmogorov* realized that [[Measure Theory]] could be applied to give a rigorous grounding to probability theory, while also resolving certain paradoxes.

>[!tldr]
>- The sample space is the set of all outcome I have.
>- [[Measure Theory#^def-sigma-algebra|Sigma algebra]] $\mathcal{F}$ is the collection of all the events that I can actually measure.
>- $P$ is the [[Measure Theory#^def-measure|measure]] that give me the "size" or "likelihood" of each event, with $\sigma$-algebra being the domain of the measure (function) $P$ and $[0, 1]$ is the range.

#### The Probability Space: $(\Omega, \mathcal{F}, P)$

The core of Kolmogorov's framework is the **probability space**, a specific type of [[Measure Theory#Measure Space|measure space]] that consists of three components: Sample space ($\Omega$), Sigma Algebra $\mathcal{F}$, Probability measure $P$.

###### Sample Space $(\Omega)$

This is a non-empty set representing all possible outcomes of a random experiment.
For a coin toss, $\Omega = \{H, T\}$. For rolling a standard die, $\Omega= \{1, 2, 3, 4, 5, 6\}$.

###### Sigma Algebra

>[!cite] Sigma Algebra in Probability Theory
>$\sigma$-algebra $\mathcal{F} \subseteq \mathcal{P}(\Omega)$, is the collection (set) of all possible subsets of the sample space that can be assigned a meaningful probability.
>$\mathcal{F}$ must satisfy three rules:
>- **Contains the sample space:** $\Omega \in \mathcal{F}$
>	The entire set of outcomes, $\Omega$, must be an event in $\mathcal{F}$
>- **Closed under Complement**: $E \in \mathcal{F} \implies E^{C} \in \mathcal{F}$
>	If $E$ is an event in $\mathcal{F}$, then its complement, $E^{C}$ (all outcomes in $\Omega$ that are not in $E$), must also be in $\mathcal{F}$.
>- **Closed under Countable Union**: $E_{1}, E_{2}, E_{3}... \in \mathcal{F} \implies \bigcup_{i=1}^{\infty}E_{i} \in \mathcal{F}$
>	If $E_{1}, E_{2}, E_{3}...$ is a countable sequence of events in $\mathcal{F}$, then their union must also be an event in $\mathcal{F}$

The pair $(\Omega, \mathcal{F})$ is called a **measurable space**, and it defines which sets of outcomes can be assigned meaningful probabilities.

***An event can now be defined as an element of the sigma-algebra.***

###### Probability Measure $(P)$

This is a function that assigns a probability to each event in the sigma-algebra $\mathcal{F}$ and must satisfy Kolmogorov's three axioms.

>[!cite] Kolmogorov's Axioms
>For a given measurable space $(\Omega, \mathcal{F})$ , a function $P: \mathcal{F} \rightarrow [0, 1]$ is a **probability measure** if it satisfies the following three axioms:
>
>- **Axiom 1: Non-negativity**  $$P(E) \geq 0 \  \forall \  E \ \in \mathcal{F}$$
>
>- **Axiom 2: Normalization**  $$P(\Omega) = 1$$
>
>- **Axiom 3: Countable Additivity** $$P\Big(\bigcup_{i=1}^{\infty}E_{i}\Big) = \sum_{i=1}^{\infty} P(E_{i}) \ \ \text{where} \ \ E_{i} \cap E_{j} = \phi \ \  \text{for} \ \ i \neq j $$

- Axiom 1: The probability of any event is a non-negative real number.
- Axiom 2: The probability of the entire sample space is 1. This is the assumption of unit measure, signifying that at least one of the possible outcomes must occur.
- Axiom 3: For any countable sequence of *mutually exclusive* events $E_{1}, E_{2}, E_{3} ... \in \mathcal{F}$, the probability of their union is equal to the sum of their individual probabilities.

## Discrete Probability Measure

In a discrete setting, defining probabilities on the "atomic" events (singletons) is fully sufficient to define the entire measure. I can rigorously define a measure on the entire $\sigma$-algebra solely by assigning probabilities to individual outcomes (singletons).

For a discrete space (where the set $X$ is countable, e.g., integers or a finite list):
- **The Generator:** The collection of singletons $\mathcal{G} = \{ \{x_1\}, \{x_2\}, \{x_3\}, \dots \}$ generates the entire power set $\mathcal{P}(X)$ as its $\sigma$-algebra.
- **Countable Additivity:** Since any set $A$ in this space is just a countable union of these single points ($A = \bigcup_{x \in A} {x}$), the third axiom of measure theory (Countable Additivity) forces the measure of $A$ to be the sum of the measures of its points.

$$\mu(A)=\sum_{x \in A} \mu(\{x\})$$

I have no choice. *Once I set the values for the singletons, the math forces the value for every other possible set.*

>[!example] Example: Rolling a Die
>- Sample Space: $\Omega = \{ 1, 2, 3, 4, 5, 6 \}$
>- $\sigma$-algebra $\mathcal{F} = \mathcal{P}(\Omega)$, its nothing but the power set in this case. That is, sigma algebra generated by the "elementary events"  $\{1\}, \{2\},  \{3\},  \{4\},  \{5\},  \{6\}$.
>- Probability measure $P$ can be defined uniquely by assigning probability to each elementary event. $P({1}) =P({2}) =P({3}) =P({4}) =P({5}) =P({6}) = \frac{1}{6}$
>- Once I have done that, any other event (element of $\mathcal{F}$), can be assigned a probability. $P({4, 3}) = P({4}) + P({3}) = \frac{1}{6} + \frac{1}{6} = \frac{1}{3}$

>[!example] Example: Rolling two Die



This can be formalized and proved as:

>[!cite] Theorem: Unique Extension for Discrete Measure
>Let $\Omega$ be a countable (finite or countably infinite) set.
>Let $\mathcal{F} = \mathcal{P}(\Omega)$ be the power set of $\Omega$ (the $\sigma$-algebra).
>If I define a function $p: \Omega \to [0, 1]$ (a Probability Mass Function) such that:
>$$ \sum_{\omega \in \Omega} p(\omega) = 1 $$
>Then there exists a **unique** probability measure $P: \mathcal{F} \to [0, 1]$ such that for every $\omega \in \Omega$: $$P({\omega}) = p(\omega) $$

###### Proof

I define the measure $P$ for any event $A \in \mathcal{F}$ (where $A \subseteq \Omega$) as the sum of the probabilities of its constituent atoms:
$$ P(A) := \sum_{\omega \in A} p(\omega) $$
To verify that $P$ is a valid probability measure, I check the Kolmogorov Axioms:
 1. Non-negativity: For any $A \in \mathcal{F}$, since $p(\omega) \ge 0$ for all $\omega$, the sum of non-negative terms is non-negative:
$$ P(A) = \sum_{\omega \in A} p(\omega) \ge 0 $$
2. Normalization (Unit Measure): The measure of the entire space is the sum over all possible outcomes, which we defined to be 1:
$$ P(\Omega) = \sum_{\omega \in \Omega} p(\omega) = 1 $$
 3. Countable Additivity: Let $A_1, A_2, \dots$ be a countable collection of **disjoint** sets. I need to show $P(\bigcup A_i) = \sum P(A_i)$. Since the sets are disjoint, any element $\omega \in \bigcup A_i$ belongs to exactly one $A_k$. By the absolute convergence of non-negative series, we can rearrange the summation order:$$
\begin{aligned}
P\left(\bigcup_{i=1}^\infty A_i\right) &= \sum_{\omega \in \bigcup A_i} p(\omega) \\
&= \sum_{i=1}^\infty \left( \sum_{\omega \in A_i} p(\omega) \right) \quad \text{(Grouping by set)} \\
&= \sum_{i=1}^\infty P(A_i)
\end{aligned}
$$
The function $P$ satisfies all axioms. Thus, the simple assignment of probabilities to singletons $p(\omega)$ **uniquely determines** the measure for the entire system.