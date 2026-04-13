Here is the complete topic 4 — the co-occurrence ratio intuition — built up exactly as the lecture presents it. :contentReference[oaicite:0]{index=0}

***

## The Co-occurrence Ratio Intuition

This is the conceptual foundation of GloVe. No math yet — just an argument about what information actually matters.

***

## Setting Up the Question

Take two related words: **"ice"** and **"steam"**. Both involve water. Both involve thermodynamics. But they represent fundamentally different physical states — one is frozen, one is gaseous.

The question GloVe starts with is: *how can we, purely from co-occurrence statistics, figure out what makes ice and steam different from each other, and what makes them similar?*

To answer this, take four **probe words** — words you use to interrogate the relationship between "ice" and "steam":

| Probe word $w_k$ | Relationship |
|---|---|
| **solid** | Related to ice, not to steam |
| **gas** | Related to steam, not to ice |
| **water** | Related to both ice and steam |
| **fashion** | Related to neither |

***

## Step 1: Look at Raw Co-occurrence Probabilities

Define $P(w_k \mid w_i)$ as the probability that probe word $w_k$ appears in the context of word $w_i$. This is just the count of how many times $w_k$ and $w_i$ co-occurred, divided by the total count of all words that co-occurred with $w_i$:

$$
P(w_k \mid w_i) = \frac{X_{ik}}{\sum_{j} X_{ij}}
$$

where $X_{ij}$ is the raw co-occurrence count between words $i$ and $j$.

From a 6-billion-token corpus, the actual values from the original paper are:

| Probe word $w_k$ | $P(w_k \mid \text{ice})$ | $P(w_k \mid \text{steam})$ |
|---|---|---|
| solid | $1.9 \times 10^{-4}$ | $2.2 \times 10^{-5}$ |
| gas | $6.6 \times 10^{-5}$ | $7.8 \times 10^{-4}$ |
| water | $3.0 \times 10^{-3}$ | $2.2 \times 10^{-3}$ |
| fashion | $1.7 \times 10^{-5}$ | $1.8 \times 10^{-5}$ |

Now look at what these raw probabilities tell you:

- **solid**: $P(\text{solid} \mid \text{ice}) = 1.9 \times 10^{-4}$. Is that high or low? Hard to say in isolation.
- **water**: $P(\text{water} \mid \text{ice}) = 3.0 \times 10^{-3}$. This is higher — but is it high because water is meaningfully related to ice, or just because "water" is a very common word in general?
- **fashion**: $P(\text{fashion} \mid \text{ice}) = 1.7 \times 10^{-5}$. Very small — but is this small because fashion is irrelevant, or because "fashion" is just a rare word?

**The problem with raw probabilities**: they conflate two separate signals — *how frequent the word is overall* and *how specific its relationship to "ice" is*. A common but irrelevant word like "the" would score very high just because it appears everywhere. A rare but highly relevant word might score low just because it's uncommon. Raw probabilities are noisy.

***

## Step 2: Take the Ratio

Now compute the ratio of co-occurrence probabilities:

$$
\text{ratio} = \frac{P(w_k \mid \text{ice})}{P(w_k \mid \text{steam})}
$$

| Probe word $w_k$ | $P(w_k \mid \text{ice})$ | $P(w_k \mid \text{steam})$ | **Ratio** |
|---|---|---|---|
| solid | $1.9 \times 10^{-4}$ | $2.2 \times 10^{-5}$ | **8.9** |
| gas | $6.6 \times 10^{-5}$ | $7.8 \times 10^{-4}$ | **0.085** |
| water | $3.0 \times 10^{-3}$ | $2.2 \times 10^{-3}$ | **1.36** |
| fashion | $1.7 \times 10^{-5}$ | $1.8 \times 10^{-5}$ | **0.96** |

Now read the pattern:

***

### Case 1 — Probe word is related to ice but not steam: ratio >> 1

**"solid"** co-occurs frequently with "ice" and rarely with "steam". The ratio is **8.9**. The large value cleanly signals: *this probe word is specific to ice*.

***

### Case 2 — Probe word is related to steam but not ice: ratio << 1

**"gas"** co-occurs rarely with "ice" and frequently with "steam". The ratio is **0.085** — much less than 1. The small value cleanly signals: *this probe word is specific to steam*.

***

### Case 3 — Probe word is related to both: ratio ≈ 1

**"water"** co-occurs frequently with both ice and steam. $P(\text{water} \mid \text{ice})$ is large, but so is $P(\text{water} \mid \text{steam})$. They cancel out in the ratio → **1.36**, close to 1. The ratio says: *this word does not help us distinguish ice from steam*. And that's correct — water is not diagnostic of the difference.

***

### Case 4 — Probe word is related to neither: ratio ≈ 1

**"fashion"** co-occurs rarely with ice and rarely with steam. Both probabilities are tiny — but they are roughly equally tiny. The ratio is **0.96**, again close to 1. The ratio says: *this word is also not diagnostic*. And again, that's correct.

***

## The Key Insight

The ratio $\frac{P(w_k \mid w_i)}{P(w_k \mid w_j)}$ has a remarkable property:

- Words that are common but non-discriminative (like "water") appear frequently in both numerator and denominator — they divide to ≈ 1, contributing nothing.
- Words that are rare but non-discriminative (like "fashion") are small in both numerator and denominator — they also divide to ≈ 1, contributing nothing.
- **Only words that are differentially related to $w_i$ vs $w_j$** produce a ratio that is meaningfully above or below 1.

This is what raw probabilities cannot do. Raw $P(\text{water} \mid \text{ice}) = 0.003$ is large and would pull "ice" toward "water" in any similarity measure. But in the ratio, water's equal association with both ice and steam is correctly neutralized. The ratio isolates the **distinguishing** signal.

***

## What This Means for Learning Word Vectors

The ratio encodes three types of relationships, cleanly and simultaneously:

| Ratio value | Meaning |
|---|---|
| **>> 1** | $w_k$ is specific to $w_i$ (ice-like, solid-like) |
| **<< 1** | $w_k$ is specific to $w_j$ (steam-like, gas-like) |
| **≈ 1** | $w_k$ is neutral — equally related to both or to neither |

This is information you want your word vectors to encode. The ratio $\frac{P_{ik}}{P_{jk}}$ depends on three words — $w_i$, $w_j$, and $w_k$ — and captures the full relational geometry between them.

So GloVe's design principle becomes: **build a model whose word vectors, through some function $F$, produce exactly this ratio**:

$$
F(v_i,\ v_j,\ u_k) = \frac{P_{ik}}{P_{jk}}
$$

The derivation of the GloVe objective function is simply the process of working out what $F$ must look like, what constraints it must satisfy, and what loss function makes the learned vectors match this target ratio. That is exactly what the next topic — the GloVe objective derivation — covers.

I now have the exact lecture slides and the full GloVe paper. Here is the complete derivation and all remaining topics. :contentReference[oaicite:0]{index=0}

***

## Deriving the GloVe Objective Function

### Starting Point: What Should the Vectors Do?

From the ratio intuition, the goal is a model where word vectors capture the ratio $\frac{P_{ik}}{P_{jk}}$. In the most general form, some function $F$ of the vectors for words $i$, $j$, and $k$ should equal this ratio:

$$
F(w_i,\ w_j,\ \tilde{w}_k) = \frac{P_{ik}}{P_{jk}}
$$

Here $w_i, w_j$ are the **target vectors** (from matrix $W$) and $\tilde{w}_k$ is the **context vector** (from matrix $W'$) — the same two-matrix structure as word2vec.

Now we ask: what must $F$ look like? Three constraints nail it down completely.

***

### Constraint 1: Vector Differences Encode Meaning

The ratio is about the **difference** between word $i$ and word $j$ in relation to word $k$. Vector spaces are inherently linear, so the natural way to express a difference between two words is through their vector difference. So restrict $F$ to only depend on $w_i - w_j$:

$$
F(w_i - w_j,\ \tilde{w}_k) = \frac{P_{ik}}{P_{jk}}
$$

***

### Constraint 2: The Argument Must Be a Scalar

$F$ takes two vectors and returns a scalar (the ratio). To avoid $F$ mixing vector dimensions in messy ways, take the dot product first:

$$
F\!\left((w_i - w_j)^\top \tilde{w}_k\right) = \frac{P_{ik}}{P_{jk}}
$$

***

### Constraint 3: Symmetry — Target and Context Are Interchangeable

In a word-word co-occurrence matrix, the distinction between "word" and "context word" is arbitrary. "cat" appearing in the context of "sat" is the same event as "sat" appearing in the context of "cat". So the model must be symmetric: swapping $w \leftrightarrow \tilde{w}$ should leave the model unchanged.

For this symmetry to hold, $F$ must be a **homomorphism** between addition and multiplication — meaning it must turn differences into ratios. The only continuous function with this property is the exponential:

$$
F = \exp
$$

Applying this:

$$
\exp\!\left((w_i - w_j)^\top \tilde{w}_k\right) = \frac{P_{ik}}{P_{jk}}
$$

$$
\frac{\exp(w_i^\top \tilde{w}_k)}{\exp(w_j^\top \tilde{w}_k)} = \frac{P_{ik}}{P_{jk}}
$$

This means the individual terms must satisfy:

$$
\exp(w_i^\top \tilde{w}_k) = P_{ik} = \frac{X_{ik}}{X_i}
$$

Taking the log of both sides:

$$
w_i^\top \tilde{w}_k = \log P_{ik} = \log X_{ik} - \log X_i \quad \cdots (1)
$$

***

### Combining Both Directions

Equation (1) is asymmetric — it treats $i$ as target and $k$ as context. But the model should be symmetric. Write the same equation with the roles swapped — $j$ as target and $i$ as context:

$$
w_j^\top \tilde{w}_i = \log X_{ij} - \log X_j \quad \cdots (2)
$$

Note that $X_{ij} = X_{ji}$ (co-occurrence is symmetric), so $\log X_{ik} = \log X_{ki}$. Add equations (1) and (2):

$$
2\, w_i^\top w_j = 2\log X_{ij} - \log X_i - \log X_j
$$

$$
w_i^\top w_j = \log X_{ij} - \frac{1}{2}\log X_i - \frac{1}{2}\log X_j
$$

***

### Absorbing the Row Sums Into Bias Terms

The terms $\frac{1}{2}\log X_i$ and $\frac{1}{2}\log X_j$ depend only on word $i$ and word $j$ independently — they are word-specific constants, not anything about the pair. Rather than computing them from raw counts, GloVe makes them **learnable scalar biases** $b_i$ and $b_j$:

$$
w_i^\top \tilde{w}_j = \log X_{ij} - \frac{1}{2}\log X_i - \frac{1}{2}\log X_j
\approx \log X_{ij} - b_i - b_j
$$

Rearranging:

$$
\boxed{w_i^\top \tilde{w}_j + b_i + b_j = \log X_{ij}}
$$

This is the **GloVe prediction equation**. The dot product of two word vectors, plus two scalar biases, should equal the log of their co-occurrence count. This is what the model tries to achieve for every pair $(i, j)$ in the vocabulary.

***

### The Loss Function (Unweighted)

Cast this as a least-squares regression problem — for every pair $(i, j)$, minimize the squared difference between the left side (what the model predicts) and the right side (what the data says):

$$
\mathcal{L} = \min_{w_i, \tilde{w}_j, b_i, b_j}\ \sum_{i,j=1}^{|V|} \left(w_i^\top \tilde{w}_j + b_i + b_j - \log X_{ij}\right)^2
$$

The learnable parameters are: $w_i$ (rows of $W$), $\tilde{w}_j$ (rows of $W'$), $b_i$ (scalar bias per target word), and $b_j$ (scalar bias per context word). That's all.

But this loss has a serious problem.

***

## The Weighting Function $f(X_{ij})$

### The Problem With Equal Weighting

The unweighted loss treats every co-occurrence pair identically. It penalizes a wrong prediction for the pair ("the", "a") — which co-occurred 10 million times — exactly as much as a wrong prediction for ("cat", "astronaut") — which co-occurred 0 times. This creates two problems:

**Problem 1 — Rare co-occurrences are noisy.** A pair that co-occurred only once or twice has very little statistical signal — it might be a fluke. Yet the unweighted loss gives it exactly as much training influence as a pair that co-occurred 50,000 times.

**Problem 2 — Frequent co-occurrences dominate.** Pairs involving very common words like "the" and "of" have enormous $X_{ij}$ values. Their large log counts make their squared errors large, and they dominate the gradient signal — drowning out the rarer but more semantically informative pairs.

***

### The Three Properties $f$ Must Have

The weighting function $f(X_{ij})$ must satisfy three properties:

**1.** $f(0) = 0$ — pairs that never co-occurred should contribute zero to the loss. The logarithm $\log X_{ij}$ is undefined at 0, so pairs with $X_{ij} = 0$ must be suppressed entirely.

**2.** $f(x)$ must be **non-decreasing** — rare co-occurrences should get less weight than frequent ones. More evidence → more weight.

**3.** $f(x)$ must be **relatively small for large $x$** — very frequent co-occurrences should not be overweighted. The function must level off so that "the" and "a" don't dominate everything.

***

### The Weighting Function

The function that satisfies all three properties is a **piecewise power function with a cutoff**:

$$
f(x) =
\begin{cases}
\left(\dfrac{x}{x_{\max}}\right)^\alpha & \text{if } x < x_{\max} \\[6pt]
1 & \text{otherwise}
\end{cases}
$$

**How it behaves:**

- For $x = 0$: $f(0) = 0$ — zero-count pairs are fully suppressed
- For $0 < x < x_{\max}$: $f(x)$ rises from 0 to 1 as a smooth power curve — rare pairs get small weight, frequent pairs get larger weight
- For $x \geq x_{\max}$: $f(x) = 1$ — the weight is capped at 1. No pair, no matter how frequent, can have more influence than this ceiling

The parameters $x_{\max}$ and $\alpha$ are set empirically. The original paper fixes $x_{\max} = 100$ and finds that $\alpha = \frac{3}{4}$ gives the best performance — notably the same fractional exponent used in word2vec's negative sampling distribution.

***

### The Final GloVe Objective

Plug $f(X_{ij})$ into the loss:

$$
\boxed{\mathcal{L} = \sum_{i,j=1}^{|V|} f(X_{ij})\left(w_i^\top \tilde{w}_j + b_i + b_j - \log X_{ij}\right)^2}
$$

This is the complete GloVe training objective. Minimize this over all word pairs using gradient descent (specifically AdaGrad in the original paper). The co-occurrence matrix $X$ is computed once before training begins and held fixed throughout. Only $w_i$, $\tilde{w}_j$, $b_i$, $b_j$ are updated.

***

## Properties of GloVe

**Fast training** — the co-occurrence matrix is built with a single corpus scan. Training optimizes over the nonzero entries of $X$ — which in practice is far smaller than $|V|^2$ because most word pairs never co-occur. This makes GloVe significantly faster than word2vec on the same corpus.

**Scalable to huge corpora** — training complexity depends on the number of nonzero entries in $X$, not directly on corpus size. GloVe was trained on a 42-billion-token Common Crawl corpus in the original paper, achieving 75% accuracy on the word analogy task.

**Good performance even on small corpora** — unlike word2vec, which needs billions of tokens to learn robust vectors, GloVe captures global co-occurrence statistics efficiently and produces strong embeddings even from smaller datasets. This is because it processes every co-occurrence pair as an aggregate count, not as individual training instances.

***

## Interesting Properties of Word Embeddings

### Word Analogy Tests

After training, GloVe vectors support linear arithmetic that captures relational structure. The analogy "Beijing is to China as Delhi is to __?" is solved by:

$$
\vec{v}_{\text{Delhi}} - \vec{v}_{\text{Beijing}} + \vec{v}_{\text{China}} \approx \vec{v}_{\text{India}}
$$

The direction $\vec{v}_{\text{Beijing}} \to \vec{v}_{\text{China}}$ encodes the "capital of" relationship. Translating Delhi by the same vector lands near India. The original GloVe paper achieved **75% accuracy** on a 19,544-question analogy test.

***

### Bias in Embeddings

Word embeddings absorb and replicate biases present in the training text. Because embeddings are trained on human-generated text, they reflect the associations people actually write — including social stereotypes:

- father : computer programmer :: mother : **homemaker**
- man : doctor :: woman : **nurse**
- man : financial advisor :: woman : **homemaker**

These are not bugs inserted by the model — they emerge automatically from the statistics of the corpus. The model has no concept of fairness; it just faithfully encodes what co-occurs with what. Words like "doctor" appear more frequently near "man" in historical text, and "nurse" more frequently near "woman", so the embedding space reflects that gap. This is now an active research area: how to debias embeddings while preserving their semantic utility.

***

### Diachronic (Temporal) Word Embeddings

Train separate GloVe models on text from different decades — for example, the New York Times corpus split by decade from 1950 to 2010. Track how the embedding of a word moves through the vector space over time.

Classic example: **"broadcast"**

- In the 1850s, "broadcast" was an agricultural term — "to broadcast seeds" meant scattering them widely across a field. Its nearest neighbors were "sow", "scatter", "field".
- By the 1920s, with the rise of radio, "broadcast" migrated — its nearest neighbors became "radio", "transmit", "station".
- By the 1960s–70s, it fully shifted to television — nearest neighbors became "television", "network", "program".

Another example: **"gay"** — which shifted from primarily meaning "joyful" in early 20th-century text to its modern meaning by the late 20th century. The vector for "gay" literally moves in embedding space across decades, and its nearest neighbors change accordingly.

This technique turns word embeddings into a **lens for studying cultural and linguistic change** — a field now called computational historical linguistics or diachronic NLP.