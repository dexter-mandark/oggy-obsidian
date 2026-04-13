---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Shannon's Character Guessing Game

>[!cite] Entropy
>In information theory, **entropy** $H$ measures how uncertain or surprising a random variable is. It is defined as the average amount of information I gain by observing the outcome of a random variable. The more unpredictable a variable is, the higher its entropy, the more information each observation gives me.
>
>For a continuous random variable $X$ with probability density function $p(x)$, the **differential entropy** is: $$H(X) = -\int_{-\infty}^{\infty} p(x) \ln p(x)\, dx$$
>This measures the average "surprise" per observation of $X$.
>Entropy is the mathematical measure of **uncertainty or unpredictability**.
>
>- Entropy is **zero** when an outcome is certain (probability = 1, no surprise). No matter how many times I observer the outcome, its always the same (constant).
>- Entropy is **maximized** when the distribution is perfectly uniform, when all outcomes are equally likely (maximum surprise).
> 
^def-entropy


In the character (letter) guessing game, I have to predict the text letter by letter. For a 27-symbol alphabet: 26 letters + the space character. Given a letter 'T', I gotta guess the next letter.

Shannon being the chad he was, forced his wife Mary to play this stupid game, and recorded the **number of guesses needed per character** and averaged them across the whole passage. The average came out to roughly **3 to 4 guesses**. *This average isn't random, it is the entropy of the 27-symbol ($\approx$ English) language.*

Shannon entropy cab be defined for the 27 symbol alphabet as:
$$H(X) = -\sum_{x} p(x) \log_2 p(x)$$
where $p(x)$ is the probability of each possible outcome. The base-2 logarithm means entropy is measured in **bits**, the minimum number of yes/no questions needed to identify the answer (is the next character 'T', yes or no).

###### The Baseline (Uniform Case)

If all 27 characters were equally probable (i.e., no patterns, no structure in English), then each character has probability $p= \frac{1}{27}$, and the entropy is:

$$H(X) = -\sum_{x=1}^{27} \frac{\log_2 (\frac{1}{27})}{27} = -\log_2 \left(\frac{1}{27}\right) \approx 4.75 bits$$

This means I'd need roughly **4–5 guesses on average** in a worst-case, structure-less language. This is the **maximum possible entropy** for a 27-symbol alphabet, as all outcomes are equally likely.

###### The Reality (Structured English)

English is _not_ uniform. The letter 'E' appears $\sim13\%$ of the time; 'Z' appears $\sim 0.07\%$. More powerfully, after seeing "TH", I know the next character is almost certainly "E" or a space, near zero uncertainty. This **structure and context dramatically reduce entropy**.
Every pattern, every grammar rule, every word frequency difference in English *lowers the entropy below that 4.75 ceiling.*


Shannon calculated that the true entropy of English is about **2.62 bits per character**, not 4.75. This means on average, only 2-3 guesses should be needed in theory. The game experiment gave 3–4, which is slightly higher than the theoretical 2.62 because Mary was a woman (obviously dumb, not an optimal predictor).

| Scenario                          | Entropy (bits) | What it means                             |
| --------------------------------- | -------------- | ----------------------------------------- |
| **Coin flip**                     | 1 bit          | 1 yes/no question to know the answer      |
| **Uniform 27-character alphabet** | 4.75 bits      | ~5 guesses needed on average              |
| **Real English characters**       | 2.62 bits      | ~3 guesses needed; language has structure |
| **After "TH\_"** (context given)  | ~0.5 bits      | Almost certain it's "E"; ~1 guess         |

###### Conclusion

The character guessing game is essentially what a language model does, it tries to predict the next token given previous context. A perfect language model would have exactly the same entropy as the true language distribution. A bad model assigns near-equal probabilities to all words, inflating entropy. This is precisely why **perplexity** (which is directly derived from entropy as $2^H$) is the standard evaluation metric for language models, the lower the perplexity, the better the model has learned the structure of the language.

Shannon's game, in short, proved **that language is statistically predictable, and that predictability is quantifiable**, which is the entire philosophical foundation on which statistical language models are built.

## Fundamental Terminologies

The first question is: *The fuck am I assigning probabilities to?*. Before I can define a language model, I first need to establish the objects it operates on.

>[!cite] Alphabet
> An **alphabet** $\Sigma$ is a finite, non-empty set of elementary symbols. In NLP, this could be:
> - The set of Unicode characters: $\Sigma = \{a, b, c, \dots, z, \text{space}, \text{comma}, \dots\}$
> - A set of words: $\Sigma = \{\text{"the"}, \text{"cat"}, \text{"sat"}, \dots\}$
> 
> The alphabet is the **lowest-level unit** that the system recognizes. Everything else is built on top of it.
^def-nlp-alphabet

>[!cite] Token
>Formally a token is any element $w \in \Sigma$.
>A token is a single element drawn from the alphabet $\Sigma$, its the basic unit the model operates on. Tokens can be:
> - **Characters**: `h`, `e`, `l`, `l`, `o`
> - **Words**: `hello`, `world`
> - **Subwords**: `hel`, `lo`, used by LLMs
> - **Numbers or special symbols**: `<s>` (sentence start), `</s>` (sentence end), `<UNK>` (unknown)
^def-nlp-token

>[!cite] Vocabulary
>The **vocabulary** $\mathcal{V}$ is the *complete, finite set of all distinct tokens the model knows about*. It is constructed from a training corpus and is always augmented with at least two special tokens:
> - **$\langle s \rangle$**: sentence start marker
> - **$\langle /s \rangle$**: sentence end marker
> - **$\langle \text{UNK} \rangle$**: the unknown token, representing any word not in $\mathcal{V}$
> 
> So formally: $$\mathcal{V} = \{w_1, w_2, \dots, w_{|\mathcal{V}|}\} \cup \{\langle s \rangle, \langle {/s} \rangle, \langle \text{UNK} \rangle\} \quad \forall\  w_i \in \Sigma$$
>
> The size of the vocabulary is denoted $V = |\mathcal{V}|$. For word-level models, $V$ is typically 50,000–100,000. For character-level models, $V$ is typically 27–256.
^def-nlp-vocabulary

>[!danger] Alphabet vs Vocabulary
>I can make a distinction between the two, stating the vocabulary might only include the elements (tokens) that my model knows about, and alphabet encompasses all possible elements (tokens). But the distinction is irrelevant, if you aren't being pedantic. In practice, vocabulary and alphabet are the same thing ($\mathcal{V} = \Sigma$)

---

> [!cite] String (Sequence)
>
> A **string (sequence)** over vocabulary $\mathcal{V}$ is a *finite, ordered sequence of tokens*:
>
> $$
> \mathbf{w} = (w_1, w_2, \dots, w_n) \quad \forall \ w_i \in \mathcal{V}
> $$
>
> The length of the string is $n$. The set of all possible finite strings over $\mathcal{V}$ is denoted $\mathcal{V}^*$ (Kleene star), and includes the empty string. A **sentence** is a string that begins with $\langle s \rangle$ and ends with $\langle /s \rangle$.
^def-nlp-sequence

>[!cite] Corpus
>A **corpus** $\mathcal{C}$ is a large, finite collection of sequences (natural language text that has been tokenized into sequences). It's my dataset. Formally:
> $$
> \mathcal{C} = (\mathbf{s}^{(1)}, \mathbf{s}^{(2)}, \dots, \mathbf{s}^{(M)})
> $$
>
> where each $\mathbf{s}^{(j)}$ is a sentence (a token sequence). The total number of tokens across all sentences is denoted $N = \sum_{j=1}^{M} |\mathbf{s}^{(j)}|$ and is called the **corpus size**. The corpus is assumed to be a sample drawn from the true distribution of natural language, it is the only window into that distribution.
^def-corpus

>[!cite] Count Function
>The **count function** $C(\cdot)$ maps any token sequence to its frequency in corpus $\mathcal{C}$:
>$$C(w_1, w_2, \dots, w_k) = \#\text{ times the sequence } (w_1, \dots, w_k) \text{ appears in } \mathcal{C}$$
>
>This is a non-negative integer. The count function is the **only raw information** I extract from a corpus, everything else (probabilities, models) is derived from counts.
^def-count-corpus

> [!cite] n-gram
> An **n-gram** is any contiguous sequence of exactly $n$ tokens drawn from a sentence. Formally, for a sentence $(w_1, \dots, w_L)$, the $n$-grams are:
>
> $$
> (w_i, w_{i+1}, \dots, w_{i+n-1}) \quad \text{for } i = 1, 2, \dots, L - n + 1
> $$
>
> Named instances:
> - **Unigram** ($n=1$): a single token $(w_i)$
> - **Bigram** ($n=2$): a pair $(w_i, w_{i+1})$
> - **Trigram** ($n=3$): a triple $(w_i, w_{i+1}, w_{i+2})$
> 
> The **prefix** of an n-gram $(w_1, \dots, w_n)$ is the $(n-1)$-gram $(w_1, \dots, w_{n-1})$, called the **context** or **history**.


**Tokenization (what is and is not a token)** is the process of converting raw text into a sequence of tokens. The definition of what counts as a token is a **design choice**, and that choice has deep consequences.
- Character-level models have tiny vocabularies (~27–100 symbols) but very long sequences.
- Word-level models have large vocabularies (~50,000–100,000 words) but shorter sequences.
- Subword tokenization is the middle ground that most modern LLMs have settled on.

>[!tip] Summary
>- Corpus: Collection of sentences
>- Sentence: $\langle s \rangle$ + string +  $\langle /s \rangle$
>- String (Sequence): ordered sequence of tokens
>- Vocabulary: finite set of tokens (includes $\langle s \rangle, \langle /s \rangle, \langle \text{UNK} \rangle$)

## Statistical Language Model

At its core, a language model answers the question: *"How likely is this sequence of words (tokens) to appear in natural language?"*

Given corpus $\mathcal{C}$ drawn from some true, unknown language distribution $P^*$, the task is to **estimate** a model $\hat{P}$ that is as close to $P^*$ as possible. I will never know $P^*$ exactly, I only have $\mathcal{C}$ as a finite sample of it.

> [!cite] Statistical Language model
> A statistical language model is a probability distribution $P$ over the set of all possible finite token sequences $\mathcal{V}^*$. That is:
> $$
> P : \mathcal{V}^* \to [0, 1]
> $$
> such that $P$ satisfies the axioms of probability:
> - $P(\mathbf{w}) \geq 0$ for all $\mathbf{w} \in \mathcal{V}^*$
> - $\sum_{\mathbf{w} \in \mathcal{V}^*} P(\mathbf{w}) = 1$
^def-language-model

For a string $\mathbf{w} = (w_1, w_2, \dots, w_n)$, $P(\mathbf{w})$ answers: *"What is the probability that a sentence drawn at random from natural language is exactly $\mathbf{w}$?"

The model estimates the **joint probability**:
$$\begin{align} P(\mathbf{w}) = P(w_1, w_2, \dots, w_n) &= P(w_1) \cdot P(w_2 \mid w_1) \cdots P(w_n \mid w_1, \dots, w_{n-1}) \\ \\  P(\mathbf{w}) &= \prod_{i=1}^{n} P(w_i \mid w_1, w_2, \dots, w_{i-1})\end{align}$$

Each term $P(w_i \mid w_1, \dots, w_{i-1})$ is a **conditional probability**. And the sequence $w_1, \dots, w_{i-1}$ is called the **context** or **history**, that is required to predict the $w_i$ token. I can define history at position $i$ as: $$h_i = (w_1, w_2, \dots, w_{i-1})$$
The joint probability can be stated as: $$P(w_1, \dots, w_n) = \prod_{i=1}^{n} P(w_i \mid h_i)$$
For the sentence: `the monsoon rain has begun`, the joint probability can be given as:
$$
\begin{align} P(\text{the, monsoon, rain, has, begun})  = \quad &P(\text{the}) \cdot P(\text{monsoon} \mid \text{the}) \\ & \cdot P(\text{rain} \mid \text{the, monsoon}) \\ & \cdot P(\text{has} \mid \text{the, monsoon, rain})  \\ & \cdot P(\text{begun} \mid \text{the, monsoon, rain, has})
\end{align}
$$
Now the question becomes: *How do I calculate $P(\text{the})$?*

## Estimating Probabilities From a Corpus

Maximum Likelihood Estimation is the principle of choosing model parameters $\hat{\theta}$ that maximize the probability of the observed training data $\mathcal{C}$ under the model. For a language model, the MLE estimate of a conditional probability is:

$$
\hat{P}_{\text{MLE}}(w_i \mid h_i) = \frac{C(h_i, w_i)}{C(h_i)}
$$

In the crudest sense this basically means, I count how often things appear in a large training corpus and turn counts into probabilities. For example:
$$
P(\text{monsoon} \mid \text{the}) = \frac{C\text{(the, monsoon)}}{C(\text{the})}
$$
If "the" appears 1,000,000 times in my corpus ($n(\text{the}) = 1,000,000$) and "the monsoon" appears 3,200 times ($n\text{(the monsoon)} = 3200$), then:
$$
P(\text{monsoon} \mid \text{the}) = \frac{3200}{1000000} = 0.0032
$$

MLE is clean, justified, and optimal **when counts are available**. The entire problem of statistical language modeling reduces to what happens when I don't have the count.

###### Why MLE is a natural choice ?

MLE has a clean justification from first principles. The **likelihood** of the corpus $\mathcal{C}$ under a model parameterised by conditional probabilities $\{P(w \mid h)\}$ is:

$$
\mathcal{L}(\theta ; \mathcal{C}) = \prod_{\forall w_i \in\mathcal{C}} P_\theta(w_i \mid h_i)
$$

Taking the log (which is monotone and easier to work with):

$$
\log \mathcal{L} = \sum_{\forall w_i} \log P_\theta(w_i \mid h_i) = \sum_{(h, w)} C(h, w) \cdot \log P_\theta(w \mid h)
$$

Maximizing this over $P_\theta$ subject to the constraint $\sum_w P(w \mid h) = 1$ (using a Lagrange multiplier) yields exactly:

$$
\hat{P}_{\text{MLE}}(w \mid h) = \frac{C(h, w)}{C(h)}
$$

So MLE is not an arbitrary choice, it is **provably the unique distribution that maximizes the probability of having observed this exact training corpus**.

## Where MLE Fails: Zero Probability Catastrophe

The MLE estimate for any unobserved n-gram is:
$$
\hat{P}_{\text{MLE}}(w \mid h) = \frac{C(h, w)}{C(h)} = \frac{0}{C(h)} = 0 \quad \text{whenever } C(h, w) = 0
$$
Even if one conditional probability is zero,
$$
\hat{P}(w_1, \dots, w_n) = \prod_{i=1}^{n} \hat{P}(w_i \mid h_i) = 0 \quad \text{if any single } \hat{P}(w_j \mid h_j) = 0
$$
The model assigns **zero probability to the entire sentence**, regardless of how natural and common the rest of it is.
1. **Log-likelihood collapses**: $\log \hat{P} = \sum_i \log \hat{P}(w_i \mid h_i)$ becomes $-\infty$ whenever any term is zero. Perplexity becomes infinite.
2. **Ranking is impossible**: A language model is often used to choose the best sentence among candidates. If any candidate contains an unseen n-gram, it scores zero, indistinguishable from any other zero-scored sentence.
3. **The model is not a valid probability distribution**: A proper distribution over $\mathcal{V}^*$ must assign positive probability to every string. Zero-MLE violates Kolmogorov's first axiom (non-negativity is satisfied, but normalizing across all valid sentences fails).

###### Two Types of Zeroes

It's important to distinguish why a count is zero, because not all zeros are the same problem:
- **Structural zeros (true zeros)**: Word combinations that are genuinely impossible, like two consecutive transitive verbs ("eat want") or grammatically illegal sequences. These should have zero probability, the model is correct to assign zero here.
- **Contingent zeros (false zeros)**: Valid, natural word combinations that simply never appeared in the corpus.

#### The Fundamental Problem: Data Sparsity

The sparsity problem is a **fundamental mathematical barrier** that determines the entire design of statistical language models.

The fundamental problem is: *the possible number of token-combinations I have to assign probabilities to, would always be significantly larger than token-combinations in my corpus.* And this is not a dataset size problem; it's a **combinatorial explosion** problem.

###### Parameter Space Explosion

A language model that conditions on a full context of length $k$ must, in principle, store a probability for every possible $(k+1)$-gram. The number of such parameters for a vocabulary of size $V$ is:
$$|\Theta_k| = V^k \times V = V^{k+1}$$
**Each additional token of context multiplies the parameter count by $V$. This is exponential growth in the context length $k$.** The parameter space is astronomically large for even modest context lengths.

Let the size of my vocabulary $\mathcal{V}$ be $V$, then for $V = 50,000$ :
- Possible 2-token combinations (bi-grams): $50,000^2 = 2.5 \text{ billion}$
- Possible 3-token combinations (tri-grams): $50,000^3 = 125 \text{ trillion}$
- Possible 5-token combinations (5-grams): $50,000^5$, a number so large no corpus could ever cover it.

###### The Coverage Bound

There's another side to this problem: *how many distinct $n$-grams does even the largest corpus contain?*

Two numbers:
- A corpus of $N$ total tokens can contain at most **$N-k+1$ distinct $k$-grams.** Since each sliding window of size $k$ produces one $k$-gram.
- The **total possible $k$-grams is $V^k$**.
This is where the problem is, my corpus has only a fraction of the $k$-grams that are observable (possible). Let $\mathcal{G}_k(\mathcal{C})$ denote the set of $k$-grams observed in corpus $\mathcal{C}$, for $k \geq 3$:$$|\mathcal{G}_k(\mathcal{C})| \ll V^k$$meaning **the vast majority of $k$-grams are unobserved.**

For a vocabulary of size $V = 50{,}000$, I can take a ridiculously huge corpus of size $N = 10^{12}$. In this corpus I can have at most $10^{12}-5+1\approx10^{12}$ distinct 5-grams, but the total number of possible 5-grams is $(50000)^5  \approx 3.125 \times 10^{25}$. That is three trillionths of one percent of all possible 5-grams. The rest are unseen.

>[!cite] Coverage
>Coverage is the fraction of all possible $k$-grams over the vocabulary that are observed in the corpus.
>Let $\mathcal{G}_k(\mathcal{C})$ be the set of distinct $k$-grams observed in corpus $\mathcal{C}$ of size $N$. And let $V$ be the vocabulary size. Coverage for  is defined as:
>$$\text{Coverage}(k) = \frac{|\mathcal{G}_k(\mathcal{C})|}{V^k} = \frac{N-k+1}{V^k}$$

For $N = 10^{12}$ and $V = 50{,}000$:
- On average each bi-gram is seen, $\text{Coverage}(2) = \frac{10^{12}}{(5 \times 10^4)^2} = 400$ times.
- Each tri-gram is seen, $\text{Coverage}(3) = \frac{10^{12}}{(5 \times 10^4)^3} = 0.008 < 1\%$ of the time.
- Each 4-gram is seen, $\text{Coverage}(4) = \frac{10^{12}}{(5 \times 10^4)^4} \approx 1.6 \times 10^{-7}$ essentially zero times.

This is not a matter of needing more data, **no realistic increase in corpus size can fix this**. For $k \geq 3$ and realistic $V$ and $N$, virtually the entire parameter space of $k$-grams is unobserved.

###### The Sparse Count Reliability Problem

There is a subtler second failure mode, distinct from the zero problem. Even for n-grams that are observed, if the count is very small,  the MLE estimate is **statistically unreliable** as it has an **extremely high variance**.

Consider estimating $P(\text{begun} \mid \text{monsoon rain has})$ Suppose `monsoon rain has` appeared exactly once in training, and was followed by `begun` MLE gives: $\hat{P}(\text{begun} \mid \text{monsoon rain has}) = 1.0$. The model is completely certain that "monsoon rain has" is always followed by "begun", a wildly overconfident and wrong conclusion from a single data point.
This is the **sparse count reliability problem**: *even non-zero MLE estimates from low counts have extremely high variance.*

Formally, the MLE estimator $\hat{P} = \frac{C(h,w)}{C(h)}$ is an unbiased estimator of the true probability $P^*(w \mid h)$, but its variance is (don't worry about the maths, just run with it):

$$
\text{Var}(\hat{P}) = \frac{P^*(w \mid h)(1 - P^*(w \mid h))}{C(h)}
$$

When $C(h)$ is small (1 or 2) the variance is enormous, the estimate could easily be wrong by a factor of 10 or 100. An estimate of $\hat{P}(\text{begun} \mid \text{monsoon rain has}) = 1.0$ derived from a single occurrence is wildly overconfident and almost certainly wrong.

## The Markov Assumption: Limiting Context Length

For the joint probability:
$$P(w_1, \dots, w_n) = \prod_{i=1}^{n} P(w_i \mid h_i)$$

Each conditional probability $P(w_i \mid h_i)$ conditions on the **entire history** $h_i = (w_1, \dots, w_{i-1})$. As the sentence grows, $h_i$ grows, the parameter space explodes as $V^{|h_i|}$. No corpus can ever densely cover this space.

The Markov assumption **throws away most of the history** and keeps only the last $k$ words. Its an **approximation** made purely to make the problem tractable.

>[!cite] Markov Property
>A stochastic process satisfies the Markov property of order $k$ if the future state $\{X_i\}_{i\geq 1}$ is conditionally independent of all states earlier than $X_{i-k}$​, given the last $k$ states.
>$$P(X_i​∣X_1​,X_2​,\cdots,X_{i−1}​)=P(X_i​∣X_{i−k}​,X_{i−k+1}​,\cdots,X_{i−1}​) \quad \forall \ i$$
>I run under the assumption that *the future is conditionally independent of the past given the last $k$ states.*
>
>The **order** $k$ is the number of most recent past states required to fully determine the conditional distribution of the next state. Basically, *how much past I need to remember for predicting the future.*
^def-markov-property

The Markov Property of Order $k$ states that the probability of the next token depends only on the last $k$ tokens, not the full history: $$P(w_i \mid w_1, w_2, \dots, w_{i-1}) \approx P(w_i \mid w_{i-k}, w_{i-k+1}, \dots, w_{i-1})$$This is an approximation, the $\approx$ is important. The full conditional and the truncated conditional are only equal if language truly has no dependencies beyond the last $k$ words, which is never strictly true.

By fixing context length to $k$, the parameter space collapses from $V^{i-1}$ (growing without bound) to $V^k$ (a fixed constant).  And the MLE estimator becomes:

$$
\hat{P}(w_i \mid w_{i-k}, \dots, w_{i-1}) = \frac{C(w_{i-k}, \dots, w_{i-1}, w_i)}{C(w_{i-k}, \dots, w_{i-1})}
$$

Both the numerator and denominator are counts of **fixed-length n-grams**, something I can actually build a finite table for and populate from a corpus.
This is the entire point of the Markov assumption: *it converts an infinite-context estimation problem into a finite-table lookup problem.*

#### The N-gram Model

An **n-gram model** is a language model that applies the Markov assumption of order $k = n - 1$. The "$n$" refers to the total sequence length (context + predicted word), while "$k$" refers to the context length alone. So a bi-gram model ($n=2$) uses a context of $k = 1$ previous word.

###### Unigram Model ($n = 1$, $k = 0$)

The **zeroth-order Markov assumption**: the probability of each word is completely independent of all previous words.

$$
P(w_1, w_2, \dots, w_N) \approx \prod_{i=1}^{N} P(w_i)
$$

The MLE estimate is just the relative frequency of each word in the corpus:

$$
\hat{P}(w_i) = \frac{C(w_i)}{N}
$$

What it gets right: Every word has a nonzero probability (as long as it appeared in training). **No sparsity problem** at all the parameter table has exactly $V$ entries.

What it gets catastrophically wrong: It **treats language as a bag of words**, completely ignoring order. Under a unigram model:

$$
P(\text{cat the mat sat on}) = P(\text{the cat sat on mat})
$$

Both sentences get the same probability because unigrams are just per-word counts. *The model has no notion of grammar, syntax, or word order whatsoever.*

###### Bigram Model ($n = 2$, $k = 1$)

The **first-order Markov assumption**: each word depends only on the immediately preceding word.
$$
P(w_i \mid w_1, \dots, w_{i-1}) \approx P(w_i \mid w_{i-1})
$$
The full sentence probability becomes:
$$
P(w_1, \dots, w_N) \approx \prod_{i=1}^{N} P(w_i \mid w_{i-1})
$$
With $w_0 = \langle s \rangle$ (the sentence-start marker). The MLE estimate:
$$
\hat{P}(w_i \mid w_{i-1}) = \frac{C(w_{i-1},\, w_i)}{C(w_{i-1})}
$$

For example, the sentence `the cat sat on the mat` has the probability:
$$
\begin{align} P(\text{the cat sat on the mat}) \approx \ & P(\text{the} \mid \langle s \rangle) \cdot P(\text{cat} \mid \text{the}) \cdot P(\text{sat} \mid \text{cat}) \\& \cdot P(\text{on} \mid \text{sat}) \cdot P(\text{the} \mid \text{on}) \cdot P(\text{mat} \mid \text{the}) \cdot P(\langle /s \rangle \mid \text{mat}) \end{align}
$$

Each factor is a simple bigram count ratio,  something I can look up in a table. The bigram model now **respects local word order**, "the cat" will get different probability than "cat the."

Parameter count with $V = 50{,}000$: $V^2 = 2.5$ billion entries in the bigram table. Most are zero, but the table is finite and can be stored in sparse format.

###### Trigram Model ($n = 3$, $k = 2$)

The **second-order Markov assumption**: each word depends on the two immediately preceding words.
$$
P(w_i \mid w_1, \dots, w_{i-1}) \approx P(w_i \mid w_{i-2}, w_{i-1})
$$
The full sentence probability becomes:
$$
P(w_1, \dots, w_N) \approx \prod_{i=1}^{N} P(w_i \mid w_{i-2}, w_{i-1})
$$
The MLE estimate:
$$
\hat{P}(w_i \mid w_{i-2}, w_{i-1}) = \frac{C(w_{i-2},\, w_{i-1},\, w_i)}{C(w_{i-2},\, w_{i-1})}
$$

What improves: The trigram model **captures short-range syntactic structure** much better. Quite fucking obvious.

What gets worse: The parameter space jumps to $V^3 = 125$ trillion entries. Sparsity increases dramatically. The vast majority of trigrams never appear in any corpus. (oh no, who could have seen this coming! again quite fucking obvious).

###### 4-gram and Beyond

The pattern continues: a 4-gram model ($k=3$) gives:

$$
\hat{P}(w_i \mid w_{i-3}, w_{i-2}, w_{i-1}) = \frac{C(w_{i-3}, w_{i-2}, w_{i-1}, w_i)}{C(w_{i-3}, w_{i-2}, w_{i-1})}
$$

With $V^4 \approx 6.25 \times 10^{18}$ possible parameters. And we're back to where we started, the sparsity problem hits again.

###### Building the N-gram Model: Count Matrix (Table)

Building an n-gram model in practice means constructing a **count table**.

For a bigram model, this is a $V \times V$ matrix where:
- **Row**: the context word $w_{i-1}$
- **Column**: the predicted word $w_i$
- **Cell $(r, c)$**: $C(w_r, w_c)$, how many times word $w_c$ followed word $w_r$ in the corpus.

Count Table:

|         | i   | want | to  | eat | chinese | food | lunch | spend |
| ------- | --- | ---- | --- | --- | ------- | ---- | ----- | ----- |
| i       | 5   | 827  | 0   | 9   | 0       | 0    | 0     | 2     |
| want    | 2   | 0    | 608 | 1   | 6       | 6    | 5     | 1     |
| to      | 2   | 0    | 4   | 686 | 2       | 0    | 6     | 211   |
| eat     | 0   | 0    | 2   | 0   | 16      | 2    | 42    | 0     |
| chinese | 1   | 0    | 0   | 0   | 0       | 82   | 1     | 0     |
| food    | 15  | 0    | 15  | 0   | 1       | 4    | 0     | 0     |
| lunch   | 2   | 0    | 0   | 0   | 0       | 1    | 0     | 0     |
| spend   | 1   | 0    | 1   | 0   | 0       | 0    | 0     | 0     |

The **probability table** is obtained by **row-normalizing the count table**, I simply divide each cell in a row by that row's sum (which equals $C(w_r)$, the unigram count of the context word):
$$
P(w_c \mid w_r) = \frac{C(w_r, w_c)}{C(w_r)} = \frac{\text{count table cell}}{\text{row sum}}
$$
Probability Table:

|         |i|want|to|eat|chinese|food|lunch|spend|
|---|---|---|---|---|---|---|---|---|
| i       |0.002|0.3265|0.0|0.0036|0.0|0.0|0.0|0.0008|
| want    |0.0022|0.0|0.6559|0.0011|0.0065|0.0065|0.0054|0.0011|
| to      |0.0008|0.0|0.0017|0.2838|0.0008|0.0|0.0025|0.0873|
| eat     |0.0|0.0|0.0027|0.0|0.0214|0.0027|0.0563|0.0|
| chinese |0.0063|0.0|0.0|0.0|0.0|0.519|0.0063|0.0|
| food    |0.0137|0.0|0.0137|0.0|0.0009|0.0037|0.0|0.0|
| lunch   |0.0059|0.0|0.0|0.0|0.0|0.0029|0.0|0.0|
| spend   |0.0036|0.0|0.0036|0.0|0.0|0.0|0.0|0.0|

In practice, this $V \times V$ matrix is **overwhelmingly sparse**. So, its better to store this not as a dense matrix but as a **sparse dictionary**: only the nonzero entries are kept.

>[!tip] Structural property of a $V \times V$ count matrix
>- The **row sum** of row $r$ = $C(w_r)$, the total number of times $w_r$ appeared as the **first word** in a bigram
>- The **column sum** of column $c$ = $C(w_c)$, the total number of times $w_c$ appeared as the **second word** in a bigram
>
>In any corpus, every token (except the very first and very last) appears as both the first word and the second word of exactly the same number of bigrams (sliding window baby, think!).
>
>Therefore: $$\underbrace{\sum_{v \in V} C(w,v)}_{\text{Row sum of } w}\;=\; \underbrace{\sum_{u \in V} C(u,w)}_{\text{Column sum of }w}\;=\;C(w)$$

#### Where the n-gram Model Fails

###### Accuracy-Sparsity Trade-off

The n-gram model, has to make a trade-off between accuracy and sparsity. **As $n$ increases, the model becomes more accurate but more sparse. As $n$ decreases, the model becomes less sparse but less accurate.

The sweet spot is **trigrams or 4-grams**. Beyond that, the sparsity gains outweigh the accuracy improvements. I'm conditioning on contexts so rare they've almost never been seen, so the probability estimates are dominated by smoothing rather than actual data. *Rule of thumb: if the typical $n$-gram count drops below 5-10 occurrences on average, I've gone too far.*

| Model       | Accuracy      | Sparsity      | Practical Verdict                          |
| ----------- | ------------- | ------------- | ------------------------------------------ |
| **Unigram** | Very low      | None          | Too simple; ignores all word order         |
| **Bigram**  | Moderate      | Low–Moderate  | Good baseline; often used in practice      |
| **Trigram** | Good          | Moderate–High | **Sweet spot** for classical NLP           |
| **4-gram**  | Better        | Very high     | Usable with large corpora + good smoothing |
| **5-gram+** | Marginal gain | Extreme       | Smoothing dominates; rarely worth it       |

###### The Long-Range Dependency Problem

The Markov assumption has a **fundamental, irreducible limitation**: *it cannot capture dependencies that span more than $k$ words.* Although by design, this still is a bloody problem.

Real syntactic dependencies in language span an **unbounded** number of words in principle (center-embedded sentences can be arbitrarily deep). The Markov assumption is a **hard cutoff**, it categorically ignores everything beyond position $i - k$, no matter how relevant it is.

The only way to resolve this, is to pick a different model: RNNs, LSTMs GRUs, Transformers.

## Out-of-Vocabulary (OOV) Tokens

The vocabulary $\mathcal{V}$ is built entirely from the **training corpus** $\mathcal{C}_{\text{train}}$. It is a fixed, finite set of tokens decided before training begins. The test corpus $\mathcal{C}_{\text{test}}$ , the data on which the model is evaluated on is drawn from the real world independently.

Formally, a token $w$ is **out-of-vocabulary (OOV)** if:

$$
w \notin \mathcal{V} \quad \text{but} \quad w \in \mathcal{C}_{\text{test}}
$$

The word exists and appears in test data, but the model has never seen it and has no entry for it anywhere in its count tables. And obviously under MLE if $w \notin \mathcal{V}$, then $C(w) = 0$ and $C(h, w) = 0$ for any context $h$, the entire sentence containing $w$ scores zero probability, or worse, the system crashes outright.

###### The $\langle \text{UNK} \rangle$ Token Strategy

The standard solution is to introduce a special **unknown token** $\langle \text{UNK} \rangle$ into the vocabulary and treat it as a real word with its own count statistics.

The procedure has two phases:
**Phase 1: Training:**
1. Scan the training corpus and collect every word's frequency.
2. Apply a frequency threshold $\tau$ (e.g., $\tau = 5$).
3. Every word with $C(w) < \tau$ is **replaced in-place** by $\langle \text{UNK} \rangle$ throughout the training corpus.
4. Train the n-gram model on this modified corpus, now $\langle \text{UNK} \rangle$ accumulates counts just like any other word.

**Phase 2: Testing:**
1. Any token in the test sentence that is not in $\mathcal{V}$ is replaced by $\langle \text{UNK} \rangle$ before computing probability.
2. The model then uses $C(\langle \text{UNK} \rangle)$, $C(h, \langle \text{UNK} \rangle)$ etc., which are real, nonzero counts from training

And $\langle \text{UNK} \rangle$ now has count $C(\langle \text{UNK} \rangle) = \sum_{w : C(w) < \tau} C(w)$,  the total mass of all rare words pooled together.

**The trade-off**: Every rare word loses its identity, "rizz" and "slay" and "zephyr" all collapse into the same $\langle \text{UNK} \rangle$ token. The model cannot distinguish between them.

###### Frequency Thresholding and the Lexicon

The set of words that **survive** the frequency threshold i.e., words with $C(w) \geq \tau$ is called the **lexicon** $\mathcal{L}$:
$$
\mathcal{L} = \{w \in \mathcal{V} : C(w) \geq \tau\}
$$

The *threshold $\tau$ is a hyper-parameter* tuned on a validation set. Typical values:
- $\tau = 1$: Keep all words seen even once, maximizes lexicon size, but includes very unreliable low-count words.
- $\tau = 5$:  A common practical choice, words seen fewer than 5 times have too-high-variance MLE estimates
- $10 \leq \tau \leq 20$: Conservative choice for small corpus.

The choice of $\tau$ directly affects the **OOV rate** on the test set: a higher threshold → smaller lexicon → more test words become $\langle \text{UNK} \rangle$ → higher OOV rate. It also affects sparsity in the count table: keeping only high-frequency words means every surviving word has denser co-occurrence statistics.

## Smoothing: Handling Zero Probability

MLE fails catastrophically when $C(h_i, w_i) = 0$ and the n-gram model says nothing about handling such cases. Introducing Smoothing.

**Smoothing** is the collective name for any technique that modifies probability estimates to ensure strict positivity i.e., to assign **nonzero probability to every possible token sequence**, including unseen ones.

The intuition is: **take probability mass away from observed (seen) n-grams and redistribute it to unobserved (unseen) n-grams**. The seen events are "over-trusted" by MLE and the unseen events are completely ignored. Smoothing corrects both simultaneously.
The question is *how much to take, and where to put it ?*

#### Add-1 (Laplace) Smoothing

The simplest possible smoothing: before computing any probabilities, **add 1 to every count in the table**, including all the zeros.

For **unigrams**:
$$
P_{\text{add-1}}(w_i) = \frac{C(w_i) + 1}{N + V}
$$
For **bigrams**, I add 1 to every cell in the $V \times V$ count matrix (including zeros). Each row of the count matrix now has $V$ extra counts added (one per column), so I add $V$ to each row's denominator:

$$
P_{\text{add-1}}(w_i \mid w_{i-1}) = \frac{C(w_{i-1}, w_i) + 1}{C(w_{i-1}) + V}
$$

For the general **n-gram** case:
$$
P_{\text{add-1}}(w_i \mid w_{i-n+1}, \dots, w_{i-1}) = \frac{C(w_{i-n+1}, \dots, w_i) + 1}{C(w_{i-n+1}, \dots, w_{i-1}) + V}
$$

###### The Robin Hood Problem: Over-borrowing

Add-1 is mathematically valid but practically disastrous for high-frequency n-grams. The core issue with both, this country and Add-1 is: *too many god-damn poor people around.* And if wealth (probability mass) has to be distributed to all the poor fucks (zero counts), I need to take too much from the rich, way too much.

I can easily find $P_{\text{add-1}}(w_i | w_{i-1})$ to find $C_{\text{add-1}}(w_{i-1}, w_i)$, I can simply equate the two, as the unigram count $C(w_{i-1})$ remains the same.
$$\begin{align} \frac{C_{\text{add-1}}(w_{i-1}, w_i)}{C(w_{i-1})}  
= P_{\text{add-1}}(w_i \mid w_{i-1}) \\ \\ C_{\text{add-1}}(w_{i-1}, w_i)  
= \frac{\left(C(w_{i-1}, w_i) + 1\right)\, C(w_{i-1})}  
{C(w_{i-1}) + V} \end{align}$$

Taking the same count matrix as before, the new update count matrix is:

| | i       | want | to    | eat   | chinese | food | lunch | spend |
| ------- | ---- | ----- | ----- | ------- | ---- | ----- | ----- | ------ |
| i       | 3.82 | 527.1 | 0.64  | 6.37    | 0.64 | 0.64  | 0.64  | 1.91   |
| want    | 1.17 | 0.39  | 237.9 | 0.78    | 2.73 | 2.73  | 2.34  | 0.78   |
| to      | 1.88 | 0.63  | 3.13  | 429.84  | 1.88 | 0.63  | 4.38  | 132.64 |
| eat     | 0.34 | 0.34  | 1.02  | 0.34    | 5.79 | 1.02  | 14.63 | 0.34   |
| chinese | 0.2  | 0.1   | 0.1   | 0.1     | 0.1  | 8.18  | 0.2   | 0.1    |
| food    | 6.89 | 0.43  | 6.89  | 0.43    | 0.86 | 2.15  | 0.43  | 0.43   |
| lunch   | 0.57 | 0.19  | 0.19  | 0.19    | 0.19 | 0.38  | 0.19  | 0.19   |
| spend   | 0.32 | 0.16  | 0.32  | 0.16    | 0.16 | 0.16  | 0.16  | 0.16   |

The count for `(i, want)` has dropped from 827 to 527, a significant reduction on an already well-observed bigram. That probability mass has been forcibly given to the $\sim 29,999$  unseen bigrams starting with `i`, almost none of which deserve it. *The rich as been robbed significantly, although the middle-income guys are relatively alright.*

Now consider the total probability mass stolen from **all seen** bigrams. For every row in the bigram table, all probability mass is redistributed as if there are $V$ equally probable next words instead of only the observed ones. Since the vast majority of $V$ entries in each row are zero, virtually all the stolen mass goes to truly zero-probability phantom bigrams. The model is systematically wrong about everything, not just rare events.

This is the **Robin Hood Problem**: in a vocabulary of 50,000 words, adding 1 to 50,000 zero entries steals enormous mass from the handful of high-count entries. The more sparse the model (i.e., the larger $V$ and the higher the n-gram order), the worse the distortion.
#### Add-k Smoothing

A direct generalization of Add-1: instead of adding exactly 1, add a **fractional value $k$** where $0 < k \leq 1$. For bi-grams:

$$
P_{\text{add-k}}(w_i \mid w_{i-1}) = \frac{C(w_{i-1}, w_i) + k}{C(w_{i-1}) + kV}
$$

Adding $k$ to each of the $V$ cells in a row increases the row total by $kV$, so I add $kV$ to the denominator. The formula generalizes naturally to n-grams:
$$
P_{\text{add-k}}(w_i \mid w_{i-n+1}, \dots, w_{i-1}) = \frac{C(w_{i-n+1}, \dots, w_i) + k}{C(w_{i-n+1}, \dots, w_{i-1}) + kV}
$$
######  $k$ as a Hyper-parameter

The value $k$ controls **how much mass to steal from seen events**:

- $k = 1$: Reduces to Add-1 (Laplace), too aggressive
- $k = 0.5$: **Jeffrey's prior**, still quite aggressive for large $V$
- $k = 0.01$ or smaller: Steals very little from observed events, gives tiny but nonzero probability to unseen events

$k$ is tuned on a **held-out validation set** by trying multiple values and choosing the one that *minimizes perplexity* on the validation data. This makes it a proper hyper-parameter, not derived from theory, but fit empirically.

**The residual problem**: Even optimally tuned $k$ is still distributing mass **uniformly** across all unseen n-grams. An unseen bigram like "the cuisine" gets exactly the same probability as "eat furniture". Uniform distribution across unseen events is still a crude approximation.

#### Unigram (Add-p) Smoothing

*The core flaw of both Add-1 and Add-k is that they give equal credit to all unseen n-grams.* But all unseen events are not equally likely in the real world. Consider two unseen bigrams:

The insight is: **the prior probability of a word appearing anywhere should inform how much smoothing mass it receives**. A common word like "food" deserves more smoothed probability than a rare word like "committee" even in contexts where neither has been observed.

Instead of adding a uniform constant $k$ to every cell, add an amount **proportional to the unigram probability** $P(w_i)$ of the predicted word:
$$
P_{\text{add-p}}(w_i \mid w_{i-1}) = \frac{C(w_{i-1}, w_i) + m \cdot P(w_i)}{C(w_{i-1}) + m}
$$

where:
- $P(w_i) = C(w_i) / N$ is the MLE unigram probability of the predicted word
- $m > 0$ is a concentration hyper-parameter tuned on validation data.

The numerator adds $m \cdot P(w_i)$, a quantity proportional to how common the predicted word is overall. Common words get more smoothing mass; rare words get less. The denominator adds $m$ total (since $\sum_{w_i} m \cdot P(w_i) = m \cdot 1 = m$), keeping the distribution normalized.

 Back-off Smoothing

#### Back-off Smoothing

Add-k and Add-p distribute mass uniformly or proportionally across the entire vocabulary. Back-off takes a completely different approach: **use a lower-order n-gram model as a fallback when higher-order counts are zero**.

The idea is hierarchical. For a trigram model:

$$
\hat{P}_{\text{BO}}(w_i \mid w_{i-2}, w_{i-1}) =
\begin{cases}
\dfrac{C(w_{i-2}, w_{i-1}, w_i)}{C(w_{i-2}, w_{i-1})} & \text{if } C(w_{i-2}, w_{i-1}, w_i) > 0 \\[10pt]
\alpha(w_{i-2}, w_{i-1}) \cdot \hat{P}_{\text{BO}}(w_i \mid w_{i-1}) & \text{if } C(w_{i-2}, w_{i-1}, w_i) = 0
\end{cases}
$$

where $\hat{P}_{\text{BO}}(w_i \mid w_{i-1})$ is itself defined recursively:

$$
\hat{P}_{\text{BO}}(w_i \mid w_{i-1}) =
\begin{cases}
\dfrac{C(w_{i-1}, w_i)}{C(w_{i-1})} & \text{if } C(w_{i-1}, w_i) > 0 \\[10pt]
\alpha(w_{i-1}) \cdot P(w_i) & \text{if } C(w_{i-1}, w_i) = 0
\end{cases}
$$

The cascade is: **try trigram -> fall back to bigram -> fall back to unigram**. At each level, I only descend when the higher-level count is zero.

The **back-off weights** $\alpha(w_{i-2}, w_{i-1})$ are normalization constants computed so that the conditional distribution sums to 1. They represent the leftover probability mass after the seen trigrams have taken their share.

*Back-off uses actual bigram or unigram statistics to estimate unseen events, not a uniform or proportional guess*. "eat food" gets higher probability than "eat committee" even when both trigrams are unseen, because the bigram "eat food" or the unigram "food" is more frequent.

###### When It Fails to Be a True Distribution

Back-off has a subtle but important failure mode: the $\alpha$ weights are difficult to compute correctly, and **naive back-off without proper normalization does not yield a valid probability distribution**.

If I simply substitute the lower-order probability whenever the higher-order count is zero, the total probability mass across all possible next words may not sum to 1. Some of the mass assigned to seen higher-order events effectively double-counts with the lower-order fallback. Correct implementation requires careful computation of the $\alpha$ weights to redistribute exactly the remaining mass, a non-trivial bookkeeping problem.

Additionally, back-off causes a structural discontinuity: if I add one more occurrence of a trigram to training data, its probability estimate suddenly **jumps** from the bigram back-off value to the trigram MLE value a potentially large discontinuous change. This makes back-off sensitive to small perturbations in training data.

#### Interpolation

**Always mix them up.**
Interpolation takes a philosophically different stance from back-off. Back-off asks: *"Is the high-order count nonzero? If yes, use it; if no, fall back."* Interpolation asks: *"Why choose? Always use all orders together."*

A **linearly interpolated trigram model** is:

$$
\hat{P}_{\text{interp}}(w_i \mid w_{i-2}, w_{i-1}) = \lambda_3 \cdot P(w_i \mid w_{i-2}, w_{i-1}) + \lambda_2 \cdot P(w_i \mid w_{i-1}) + \lambda_1 \cdot P(w_i)
$$

where $\lambda_1, \lambda_2, \lambda_3 \geq 0$ and $\lambda_1 + \lambda_2 + \lambda_3 = 1$. Every prediction is always a **weighted mixture** of trigram, bigram, and unigram estimates regardless of whether the higher-order counts are zero or not.

**Why this is always valid**: Since each of $P(w_i \mid w_{i-2}, w_{i-1})$, $P(w_i \mid w_{i-1})$, and $P(w_i)$ is itself a valid probability distribution over $\mathcal{V}$, their convex combination (with non-negative weights summing to 1) is also a valid distribution. Normalization is automatic. This is a key advantage over back-off.

**Why it handles unseen events**: Even if the trigram count is zero (making $P(w_i \mid w_{i-2}, w_{i-1}) = 0$), the bigram and unigram terms still contribute positive mass. The final estimate is always strictly positive as long as $\lambda_1 > 0$ and the unigram model assigns positive probability to every word.

###### How the fuck do I get $\lambda$ ?

The weights $\lambda_1, \lambda_2, \lambda_3$ are **not set by hand** they are learned from data using a held-out **validation corpus** $\mathcal{C}_{\text{val}}$ (distinct from both training and test).

The procedure is:
1. Train the raw n-gram count tables on $\mathcal{C}_{\text{train}}$
2. Hold out $\mathcal{C}_{\text{val}}$ a corpus the model has not been trained on
3. Search for $\lambda_1, \lambda_2, \lambda_3$ that maximize the likelihood of $\mathcal{C}_{\text{val}}$ under the interpolated model (equivalently, minimize perplexity on $\mathcal{C}_{\text{val}}$)
4. Subject to the constraint $\lambda_1 + \lambda_2 + \lambda_3 = 1$, $\lambda_i \geq 0$

This optimization has a clean solution via the **EM (Expectation-Maximization) algorithm**, specifically a variant called the Baum-Welch method which iteratively updates the $\lambda$ values until convergence.

###### Context-Dependent Weights

I can go a step further, **instead of having fixed global $\lambda$ values, I allow the weights to depend on the context**:
$$
\begin{align} \hat{P}_{\text{interp}}(w_i \mid w_{i-2}, w_{i-1}) &= \lambda_3(w_{i-2}, w_{i-1}) \cdot P(w_i \mid w_{i-2}, w_{i-1}) \\ &+ \lambda_2(w_{i-2}, w_{i-1}) \cdot P(w_i \mid w_{i-1})\\ &+ \lambda_1(w_{i-2}, w_{i-1}) \cdot P(w_i) \end{align}
$$

The intuition: if the context $(w_{i-2}, w_{i-1})$ has been seen many times in training, the trigram model is reliable, give $\lambda_3$ a high value. If the context is rare, fall back toward lower-order models by increasing $\lambda_2$ and $\lambda_1$. Formally, this is often implemented as a function of $C(w_{i-2}, w_{i-1})$:

$$
\lambda_3 \propto \frac{C(w_{i-2}, w_{i-1})}{C(w_{i-2}, w_{i-1}) + \gamma}
$$

where $\gamma$ is a concentration hyper-parameter. When context count is high, $\lambda_3 \to 1$; when context count is low, $\lambda_3 \to 0$ and weight shifts to lower-order terms. This is called **Jelinek-Mercer smoothing**, and it is empirically one of the best-performing classical smoothing techniques.

## Advanced Smoothing

>[!cite] Frequency of frequencies
>The **frequency of frequency**, denoted $N_c$. $$N_c = \text{number of distinct n-grams that appear exactly } c \text{ times in the corpus}$$
>$N_c$ is a histogram of histogram, it tells me **how many events are at each frequency level**.

#### Good-Turing Smoothing

The basic idea is: *I use the count of things I have seen exactly once to estimate the probability of things I have never seen.* 

If I have observed 18 birds total: 10 flamingos, 2 kingfishers, 1 Indian roller, 1 woodpecker, 1 peacock, etc.
- **MLE**: What is the probability the next bird is a woodpecker? $\frac{1}{18}$. What is the probability the next bird is a purple heron (never seen)? $\frac{0}{18} = 0$.
- **Good-Turing**: To estimate the probability of an unseen event (count = 0), look at $N_1$, how many species were seen exactly once. Here $N_1 = 3$. Good-Turing says the total probability mass of all unseen events is:
$$
P(\text{unseen}) = \frac{N_1}{N} = \frac{3}{18}
$$

That is, *$3/18$ of the total probability mass is reserved for all unseen species combined. This mass is taken from the seen events.*

The **modified count** $C^*$ for any n-gram observed $c$ times is:

$$
C^* = \frac{(c + 1) \cdot N_{c+1}}{N_c}
$$

This says: to re-estimate the count of something seen $c$ times, look at the count of things seen $c+1$ times and use that ratio. Intuitively, $N_{c+1}/N_c$ tells me how much the frequency drops as I go one level up and that ratio determines how much I discount.

**For $c = 0$** (unseen events):

$$
C^*_0 = \frac{1 \cdot N_1}{N_0}
$$
But I don't know $N_0$ (number of things never seen, it's infinite conceptually). So instead, **the probability of all unseen events collectively is simply $N_1 / N$,** divided equally among unseen events.

For the woodpecker (seen once, $c = 1$):

$$
C^*_1 = \frac{(1+1) \cdot N_2}{N_1} = \frac{2 \times 1}{3} = \frac{2}{3}
$$

So the modified probability is $\frac{2/3}{18} = \frac{1}{27}$, down from the MLE value of $\frac{1}{18}$. The count was effectively reduced from 1 to $2/3$, because *some of that mass was redistributed to unseen events.*

###### Absolute Discounting Interpolation

The AP News corpus by Church and Gale, showed the following counts:

| Original count $c$ | Good-Turing $C^∗$ | Discount $c−C^∗$ |
| ------------------ | ----------------- | ---------------- |
| 1                  | 0.446             | **0.554**        |
| 2                  | 1.260             | **0.740**        |
| 3                  | 2.240             | **0.760**        |
| 4                  | 3.240             | **0.760**        |
| 5                  | 4.220             | **0.780**        |
| 6                  | 5.190             | **0.810**        |
| 7                  | 6.210             | **0.790**        |
| 8                  | 7.240             | **0.760**        |
After $c=1$, the discount doesn't vary much, its between 0.75 and 0.81. The Good-Turing formula, despite being derived from frequency-of-frequency statistics, **empirically behaves like a fixed subtraction of ~0.75** from every count.

>[!tip]- Why Does $N_c$ Follow This Pattern? Zipf's Law
> Natural language word frequencies follow a **power-law (Zipfian) distribution**, the most frequent word appears roughly twice as often as the second most frequent, three times as often as the third, and so on.
> This structure causes the frequency-of-frequencies $N_c$ to also decay as a power law: $$ N_c \approx \frac{A}{c^\alpha} \quad \text{for constants } A > 0,\; \alpha > 1$$
> Under this model: $$\frac{N_{c+1}}{N_c} = \frac{A/(c+1)^\alpha}{A/c^\alpha} = \left(\frac{c}{c+1}\right)^\alpha = \left(1 - \frac{1}{c+1}\right)^\alpha$$
> For $\alpha = 2$ (a reasonable approximation for bigrams): $$\frac{N_{c+1}}{N_c} = \frac{c^2}{(c+1)^2}$$
> Then:  $$ \begin{align} C^* = \frac{(c+1) \cdot N_{c+1}}{N_c} = (c + 1) \cdot \frac{c^2}{(c+1)^2} = \frac{c^2}{c+1}  \\  \\ 
> c - C^* = c - \frac{c^2}{c+1} = \frac{c(c+1) - c^2}{c+1} = \frac{c}{c+1}   \end{align}$$
> 
| $c$ | $C^* = c^2/(c+1)$ | Discount $c - C^*$ |
| --- | ----------------- | ------------------ |
| 1   | 0.500             | 0.500              |
| 2   | 1.333             | 0.667              |
| 3   | 2.250             | 0.750              |
| 4   | 3.200             | 0.800              |
| 5   | 4.167             | 0.833              |
>The discount is not perfectly constant, but for $c \ge 2$ it stabilises in the range 0.67–0.83, clustering around 0.75. 

So, I should just **directly subtract a constant $d$** from every observed bigram count and redistribute the leftover mass. No need to complicate it. This is called **absolute discounting**.

Absolute discounting with interpolation defines:

$$
P_{\text{AD}}(w_i \mid w_{i-1}) = \frac{\max(C(w_{i-1}, w_i) - d,\; 0)}{C(w_{i-1})} + \lambda(w_{i-1}) \cdot P(w_i)
$$

- The first term is the discounted bigram MLE, I subtract $d$ from every observed bigram count (using $\max$ to prevent negatives for very low counts)
- The second term is the unigram fallback, the redistributed mass goes to the unigram probability of the predicted word.
- $\lambda(w_{i-1})$ is a normalizing constant that ensures the distribution sums to 1.

**The problem with this**: the fallback unigram $P(w_i)$ is just a raw frequency count. Very common words (stop words like "the", "a", "I") have enormous unigram probabilities. When I'm trying to predict the word after some context and the context is rare, the model falls back to: the next word is probably 'the'.

 What I actually want is not *"how frequent is this word overall?"* but *"how likely is this word to appear as a novel continuation in a new context?"*
#### Kneser-Ney Smoothing

Kneser-Ney is empirically **the best classical smoothing technique** and is the **standard baseline** against which neural language models are compared.

It replaces the raw unigram probability $P(w_i)$ with a **continuation probability** $P_{\text{KN}}(w_i)$:

$$
P_{\text{KN}}(w_i) = \frac{|\{w_{i-1} : C(w_{i-1}, w_i) > 0\}|}{|\{(w', w'') : C(w', w'') > 0\}|}
$$

- Numerator: The number of **unique** context words $w_{i-1}$ that the word $w_i$ has ever followed, *the number of distinct bigrams that $w_i$ completes.*
- Denominator: The total number of unique bigrams in the corpus (regardless of what words they contain)

Combining absolute discounting with the continuation probability as the fallback:

$$
P_{\text{KN}}(w_i \mid w_{i-1}) = \frac{\max(C(w_{i-1}, w_i) - d,\; 0)}{C(w_{i-1})} + \lambda(w_{i-1}) \cdot P_{\text{KN}}(w_i)
$$
The *discount $d$ is only applied to entries in the count table whose value is greater than $d$.* 
The normalizing constant $\lambda(w_{i-1})$ is computed as:

$$
\lambda(w_{i-1}) = \frac{d \cdot |\{w : C(w_{i-1}, w) > d\}|}{C(w_{i-1})}
$$

This says: the amount of probability mass redistributed from context $w_{i-1}$ is $d$ times the number of distinct words that followed $w_{i-1}$ in training, divided by $w_{i-1}$'s total count. 
If a word $w_{i-1}$ has been followed by many different words, I take away more probability mass from its observed bigrams and redistribute it; this redistributed mass is given to words that tend to appear in many different contexts, rather than just frequently overall.

- **$\lambda(w_{i−1}​)$ controls how much mass is redistributed
- **$P_{\text{KN}}(w_i)$ controls who gets it.**

## Evaluating Language Models

#### Intrinsic Evaluation: Perplexity

For two language models $L_14 and $L_2$. **Extrinsic evaluation** plugs each into a downstream task (machine translation, speech recognition, spell correction) and measures task performance. This obviously has issues, what downstream task do I use ? What if both models are shitty ? that would mask the difference between $L_1$ and $L_2$.

The solution is a metric that can be computed **directly from the language model**, without any downstream task. This metric is **perplexity**.
*Lower the perplexity, the better the model.*

>[!cite] Perplexity
>Given a test set $\mathbf{W} = w_1, w_2, \dots, w_N$ of $N$ tokens. The perplexity of $\mathbf{W}$ is defined as: $$\text{PPL}(\mathbf{W}) = P(w_1, w_2, \dots, w_N)^{-\frac{1}{N}}$$
>For a bigram model:$$\text{PPL}(\mathbf{W}) = \left(\prod_{i=1}^{N} P(w_i \mid w_{i-1})\right)^{-\frac{1}{N}}$$
>
>The $-1/N$ exponent normalizes for length so I can compare models across test sets of different sizes.
>
>So **perplexity is inversely proportional to probability**. This means:
>- If $P(\mathbf{W})$ is high (close to 1): $PPL=(1)^{\frac{-1}{N}} = 1$ ->  low perplexity 
>- If $P(\mathbf{W})$ is low (close to 0) → $PPL=(0.0001)^{\frac{-1}{N}}$ -> high perplexity.
>
>A model that assigns high probability to real sentences is a better model. 

#### Little Tangent

###### Stochastic Process

A **stochastic process** is a sequence of random variables indexed by time: $X_1, X_2, X_3, \dots$ 
In language, each $X_i$ is the random variable representing the $i$-th token in a sentence drawn from natural language. The process generates one token at a time according to some probability law. The joint distribution $P(X_1 = w_1, X_2 = w_2, \dots, X_n = w_n)$ is the probability of observing the specific sequence $(w_1, \dots, w_n)$. 

###### Stationarity

A stochastic process is **stationary** if its statistical properties **do not change over time**. Formally, if shifting the entire sequence forward by any amount $k$ does not change the joint distribution:  
  
$$  
P(X_1 = w_1, \dots, X_n = w_n) = P(X_{1+k} = w_1, \dots, X_{n+k} = w_n) \quad \forall k \geq 1
$$  
The probability of observing the phrase "I want to eat" at positions 1–4 in a sentence is the same as observing it at positions 100–103. The statistical patterns of English don't shift based on where in a document you are.
  
**Where it breaks down**: Opening sentences of a paragraph have different word distributions than closing sentences; topic shifts cause distributional changes. *Natural language is only approximately stationary* but the approximation is good enough.
###### Ergodicity

A process is **ergodic** if a **time average equals the ensemble average**.  
- **Ensemble average**: I run the process many times in parallel (imagine 1 million parallel universes each generating a different sequence of English text), then average a quantity across all those runs at one fixed time step.  
- **Time average**: I run the process **once**, for a very long time, and average a quantity across all time steps in that single run.  
  
For an ergodic process, both averages give the **same answer**, with probability 1.  

Suppose I have two coins: a fair coin (P(heads) = 0.5) and a biased coin (P(heads) = 0.9). At the start, I flip a fair coin once to choose which coin I'll use forever. Then I flip the chosen coin repeatedly.  
- **Ensemble average**: If I run this experiment in many parallel universes, half use the fair coin and half use the biased coin. The average probability of heads across all universes is $0.5 \times 0.5 + 0.5 \times 0.9 = 0.7$.  
- **Time average from a single run**: If I ended up with the biased coin, my time average will converge to 0.9. If I ended up with the fair coin, it converges to 0.5. Neither gives 0.7.  
This process is not ergodic, time averages depend on which universe I'm in, and they don't match the ensemble average.  
  
In language, ergodicity informally says *that one sufficiently long book or corpus is representative of the entire language*. I don't need to read every book ever written to estimate the statistical properties of English, one very long sample is enough.

#### Deriving Perplexity from Entropy

Shannon entropy for a discrete random variable $X$ is: $H(X) = -\sum_x p(x) \log_2 p(x)$

For a **language** (a stochastic process generating sequences), the random variable is a sequence of words $(w_1, \dots, w_n)$. The entropy of the language is:

$$
H(L) = -\sum_{\mathbf{w} \in L} P(\mathbf{w}) \log_2 P(\mathbf{w})
$$

This is the entropy of an entire sequence, it grows with length. To get a **per-token** measure, I define the **entropy rate (average entropy per token as sequences get arbitrarily long)**:
$$
h(L) = \lim_{n \to \infty} \frac{1}{n} H(w_1, w_2, \dots, w_n) = -\lim_{n \to \infty} \frac{1}{n} \sum_{\mathbf{w}} P(\mathbf{w}) \log_2 P(\mathbf{w})
$$
This requires summing over every possible sequence $\mathbf{w}$ of length $n$, The number of possible sequences of length $n$ over a vocabulary of size $V$ is $V^n$. It is computationally impossible to evaluate this sum. 

>[!cite] The Shannon-McMillan-Breiman Theorem
> The Shannon-McMillan-Breiman (SMB) theorem states:
> For a stationary ergodic stochastic process (its statistical properties don't change over time, and long enough samples are representative), the quantity $-\frac{1}{n} \log_2 P(w_1, w_2, \dots, w_n)$ converges to the entropy rate $h(L)$ **with probability 1** as $n \to \infty$, for almost any single realization of the process.
> 
> $$h(L) = \lim_{n \to \infty} -\frac{1}{n} \log_2 P(w_1, w_2, \dots, w_n)$$with probability 1, for almost any single long sequence $(w_1, \dots, w_n)$.


Using the SMB theorem, instead of summing over all possible sentences, I can estimate entropy from a **single long sequence**, which is my test corpus. The $\sum_{\mathbf{w}} P(\mathbf{w}) [\ldots]$ disappears; only $P(w_1, \dots, w_n)$ remains.

###### Cross Entropy

There are two probability distributions at play:
- $P_L$: the true, unknown probability distribution of the language (how language is actually generated)
- $P_M$: my model's probability distribution (bigram, trigram, etc.)

Nobody knows $P_L$. I can only approximate it. The **cross-entropy** between the true language and my model is:
$$
H(L, M) = -\sum_{\mathbf{w}} P_L(\mathbf{w}) \log_2 P_M(\mathbf{w})
$$
Applying SMB again - replacing the sum over all sentences with a single long test sequence:
$$
H(L, M) \approx -\frac{1}{n} \log_2 P_M(w_1, w_2, \dots, w_n)
$$

This is what *I can actually compute* from my model, on the test set.

A crucial property of cross-entropy: **the true entropy $H(L)$ is always a lower bound of the cross-entropy $H(L, M)$**:
$$
H(L) \leq H(L, M)
$$

The cross-entropy equals the true entropy plus the KL divergence between my model and the real language. The KL divergence measures how wrong my model is. A perfect model has $D_{\text{KL}} = 0$ and thus $H(L, M) = H(L)$. Every model imperfection adds extra bits and extra perplexity.

###### Perplexity = $2^H$

Ignoring the limit (working with a finite long test sentence):
$$
H(L, M) \approx -\frac{1}{n} \log_2 P_M(w_1, \dots, w_n)
$$
Raise 2 to the power of both sides:
$$
2^{H(L,M)} = 2^{-\frac{1}{n} \log_2 P_M(w_1, \dots, w_n)} = P_M(w_1, \dots, w_n)^{-\frac{1}{n}} = \text{PPL}
$$

$$
\begin{align} \text{PPL} &= 2^{H(L,\, M)} \\ &= P_M(w_1, \dots, w_n)^{-\frac{1}{n}} \end{align}
$$

**Perplexity is exactly 2 raised to the cross-entropy.** 
Since $H(L, M) \geq H(L)$, **perplexity is always at least $2^{H(L)}$**, the theoretical minimum set by the true entropy of language. 
Every bit of mismatch between my model and reality multiplies the perplexity by 2.

###### Intuition behind $2^{H}$

Entropy $H$ is the *average number of bits* needed to encode the next token. Two to the power of the number of bits is the *number of equally likely candidates* those bits can represent.

**So perplexity is literally the effective vocabulary size** the model believes it is choosing from at each step, the effective branching factor.
- Low perplexity (say 10): the model effectively considers only 10 candidates at each step, it is very certain
- High perplexity (say 10,000): the model is as confused as if it were uniformly choosing among 10,000 words, it has learned almost nothing.