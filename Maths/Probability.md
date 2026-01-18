---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Sample Spaces and Events

Every probabilistic model involves an underlying process, called the **experiment**, that will produce exactly one out of several possible **outcomes**.

![Probability#invert_B](expierment-sampleSpace-probabilty.png)

>[!abstract] Sample Space
>The **set of all possible outcomes** is called the sample space of the experiment, and is denoted by $\Omega$.
>
>A sample space is a set of mutually exclusive and collectively exhaustive outcomes.
>- **Mutually Exclusive:** When the experiment is carried out, there is a unique outcome. The sample space associated with the roll of a die cannot contain “1 or 3” as a possible outcome.
>- **Collectively Exhaustive:** No matter what happens in the experiment, I should always obtain an outcome that has been included in the sample space.
>
>Sample Space can be a finite set, countable infinite or uncountable infinite set, depending upon the experiment.

There is no restriction on what constitutes an experiment. For example,
- Finite sample space: It could be a single toss of a coin, or three tosses
- Countable Infinite sample space: An inﬁnite sequence of tosses.
- Uncountable infinite sample space: Throwing a dart on a square target and viewing the point of impact as the outcome.

>[!abstract] Event
>Any **subset of the sample space**, that is, a collection of possible outcomes, is called an event.
>
>If the outcome of an experiment is contained in event $E$, then I say *event $E$ has occurred*.

For two events $E$ and $F$,
- Event $E \cup F$ will occur if either $E$ or $F$ occurs.
- Event $E \cap F$ will occur if both $E$ and $F$ occur.
- Events $E$ and $F$ are said to be **mutually exclusive** if and only if $E \cap F = \phi$

## Probability as Relative Frequency

One way of deﬁning the probability of an event is in terms of its relative frequency.

>[!abstract] Probability as relative frequency
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
>- [[Measure Theory#^def-sigma-algebra|Sigma algebra]] $\mathcal{F}$ is the set of events that I can actually measure.
>- $P$ is the [[Measure Theory#^def-measure|measure]] that give me the "size" or "likelihood" of each event, with $\sigma$-algebra being the domain of the measure (function) $P$ and $[0, 1]$ is the range.

#### The Probability Space: $(\Omega, \mathcal{F}, P)$

The core of Kolmogorov's framework is the **probability space**, a specific type of measure space that consists of three components.

###### Sample Space $(\Omega)$

This is a non-empty set representing all possible outcomes of a random experiment.
For a coin toss, $\Omega = \{H, T\}$. For rolling a standard die, $\Omega= \{1, 2, 3, 4, 5, 6\}$.

###### Sigma Algebra

>[!abstract] Sigma Algebra in Probability Theory
>$\sigma$-algebra $\mathcal{F} \subseteq P(\Omega)$, is the collection (set) of all possible subsets of the sample space that can be assigned a meaningful probability.
>$\mathcal{F}$ must satisfy three rules:\
>- **Contains the sample space:** $\Omega \in \mathcal{F}$
>	The entire set of outcomes, $\Omega$, must be an event in $\mathcal{F}$
>- **Closed under Complement**: $E \in \mathcal{F} \implies E^{C} \in \mathcal{F}$
>	If $E$ is an event in $\mathcal{F}$, then its complement, $E^{C}$ (all outcomes in $\Omega$ that are not in $E$), must also be in $\mathcal{F}$.
>- **Closed under Countable Union**: $E_{1}, E_{2}, E_{3}... \in \mathcal{F} \implies \bigcup_{i=1}^{\infty}E_{i} \in \mathcal{F}$
>	If $E_{1}, E_{2}, E_{3}...$ is a countable sequence of events in $\mathcal{F}$, then their union must also be an event in $\mathcal{F}$

The pair $(\Omega, \mathcal{F})$ is called a **measurable space**, and it defines which sets of outcomes can be assigned meaningful probabilities.

###### Probability Measure $(P)$

This is a function that assigns a probability to each event in the sigma-algebra $\mathcal{F}$ and must satisfy Kolmogorov's three axioms.

#### Kolmogorov's Axioms

>[!abstract] Kolmogorov's Axioms
>For a given measurable space $(\Omega, \mathcal{F})$ , a function $P: \mathcal{F} \rightarrow [0, 1]$ is a **probability measure** if it satisfies the following three axioms:
>
>- **Axiom 1: Non-negativity**  $$P(E) \geq 0 \  \forall \  E \ \in \mathcal{F}$$
>
>- **Axiom 2: Normalization**  $$P(\Omega) = 1$$
>
>- **Axiom 3: Countable Additivity** $$\large{P}(\bigcup_{i=1}^{\infty}E_{i}) = \sum_{i=1}^{\infty} \large{P}(E_{i}) \ \ \text{where} \ \ E_{i} \cap E_{j} = \phi \ \  \text{for} \ \ i \neq j $$

###### The three Axioms

- Axiom 1: The probability of any event is a non-negative real number.
- Axiom 2: The probability of the entire sample space is 1. This is the assumption of unit measure, signifying that at least one of the possible outcomes must occur.
- Axiom 3: For any countable sequence of *mutually exclusive* events $E_{1}, E_{2}, E_{3} ... \in \mathcal{F}$, the probability of their union is equal to the sum of their individual probabilities.

