Here is the complete, detailed explanation of Part 3 — every subtopic, from the motivation for Word2Vec all the way through the geometry of word vectors. :contentReference[oaicite:0]{index=0}

***

## 3.1 Why Count-Based Methods Don't Scale

Before jumping into Word2Vec's architecture, it's worth understanding exactly *why* SVD and co-occurrence matrices were abandoned — not because they were wrong, but because they couldn't handle the real world.

The problem comes down to three hard limits:

- **Matrix size**: A corpus with 1 million unique words produces a $10^{12}$ entry co-occurrence matrix. Storing this in RAM is infeasible
- **Recomputation on new data**: Adding new documents to your corpus means rebuilding the entire matrix and re-running SVD from scratch — there is no incremental update
- **SVD complexity**: Full SVD on an $n \times n$ matrix is $O(n^3)$ — for a 1M-word vocabulary, this is $10^{18}$ operations, which is astronomically slow

Word2Vec (Mikolov et al., 2013) solved this by reframing the problem entirely: instead of *counting* co-occurrences and *compressing* them, **train a neural network to predict them**. The learned weights of that network become the word embeddings — and the training scales efficiently to billions of words.

***

## 3.2 Word2Vec Overview

### The Core Insight

Word2Vec trains a **shallow neural network** (just one hidden layer) on a fake/proxy task — either "predict context from a word" or "predict a word from its context". The model never actually uses the output of this task. What it keeps are the **learned hidden layer weights** — these become the word embeddings.

This is a classic example of **representation learning**: you design a task that forces the network to encode semantic meaning into its weights, then discard the task itself and keep only the weights.

### Two Vectors Per Word

Every word $w$ in the vocabulary actually has **two separate vectors**:
- $v_w$ — the **center/input vector**: used when $w$ is the target word being looked up
- $u_w$ — the **context/output vector**: used when $w$ is a context word being predicted

This is implemented as two separate weight matrices $W$ (input embedding matrix) and $W'$ (output embedding matrix), each of size $|V| \times d$ where $d$ is the embedding dimension (typically 100–300).

After training, you typically use only $v_w$ (the rows of $W$) as the final word embeddings. Sometimes the average $\frac{v_w + u_w}{2}$ is used, which can give slightly better results.

***

## 3.3 Architecture 1: Skip-gram

### The Task

Given a **center word**, predict the surrounding **context words** within a window of size $k$.

**Example**: Corpus — *"the man loves his son"*, window = 2, center word = "loves"

The model sees "loves" and must assign high probability to: "the", "man", "his", "son" — and low probability to every other word in the vocabulary.

### The Neural Network

The architecture is deliberately simple:

```
Input (one-hot, |V|×1)
        ↓  [W: |V|×d]
Hidden layer (d-dimensional, NO activation function)
        ↓  [W': d×|V|]
Output (softmax over all |V| words)
```

**Step 1** — Input: the center word "loves" is fed in as a one-hot vector. Multiplying by $W$ simply *selects* the corresponding row — this is the word's embedding vector $v_{\text{loves}}$.

**Step 2** — Hidden layer: the embedding $v_c$ passes through. There is **no non-linear activation** here — the hidden layer is purely linear. This keeps the model fast and the embeddings in a simple geometric space.

**Step 3** — Output: multiply $v_c$ by $W'$ to get a score for every vocabulary word, then apply **softmax** to convert scores to probabilities.

### The Training Objective

For a corpus of $T$ words and context window $k$, maximize the **log-likelihood** of observing the actual context words:

$$
J(\theta) = \frac{1}{T} \sum_{t=1}^{T} \sum_{\substack{-k \leq j \leq k \\ j \neq 0}} \log P(w_{t+j} \mid w_t)
$$

The probability of a context word $o$ given center word $c$ is computed via **softmax**:

$$
P(o \mid c) = \frac{\exp(u_o^\top v_c)}{\displaystyle\sum_{w \in V} \exp(u_w^\top v_c)}
$$

The numerator $\exp(u_o^\top v_c)$ is the dot product between the context vector of the target output word and the center word's embedding — high dot product = high similarity = high probability.

***

## 3.4 Architecture 2: CBOW (Continuous Bag of Words)

### The Task

The exact inverse of Skip-gram: given all the **context words**, predict the **center word**.

**Example**: Given {"the", "man", "his", "son"}, predict "loves".

### How It Works

The context words are each looked up as one-hot vectors, their embedding vectors are **averaged** (or summed), and this averaged vector is used to predict the center word via softmax:

$$
\hat{v} = \frac{1}{2k} \sum_{j=-k, j\neq 0}^{k} v_{w_{t+j}}
$$

This averaged context vector replaces $v_c$ in the softmax formula above. The rest of the computation is identical.

### Skip-gram vs. CBOW

| | **Skip-gram** | **CBOW** |
|---|---|---|
| Input | 1 center word | Multiple context words (averaged) |
| Output | Predict each context word | Predict 1 center word |
| Better for | **Rare words** | **Frequent words** |
| Training speed | Slower (generates more training pairs) | Faster |
| Accuracy | Higher in most benchmarks | Slightly lower but trains quicker |

The key intuition: Skip-gram generates $2k$ training examples per word (one per context word), giving rare words many more chances to update their embeddings. CBOW averages context information, which smooths out rare word signals but trains much faster.

***

## 3.5 The Computational Bottleneck

### Why Softmax is Catastrophically Slow

Look at the denominator of the softmax again:

$$
P(o \mid c) = \frac{\exp(u_o^\top v_c)}{\underbrace{\sum_{w=1}^{|V|} \exp(u_w^\top v_c)}_{\text{sum over ALL } |V| \text{ words}}}
$$

For every single training step, you must compute a dot product between $v_c$ and **every word's context vector** $u_w$ in the entire vocabulary. With $|V| = 1{,}000{,}000$ words and billions of training steps, this means $10^{15}$ dot product operations — completely unworkable.

The gradient updates compound this: every weight update for the output matrix $W'$ requires updating **all** $|V|$ rows, even though only one context word was actually observed as "correct." You're paying the cost of the whole vocabulary to learn from one word pair.

***

## 3.6 Negative Sampling

### The Big Idea

Instead of computing a probability over all $|V|$ words, reframe the problem as a **binary classification** task:

> *"Did this (center word, context word) pair actually appear together in the corpus?"*

- **Positive pair**: (center word, actual context word) → label = 1
- **Negative pairs**: (center word, $k$ randomly sampled words that did NOT appear in context) → label = 0

For example, if center = "loves" and context = "man", the model sees:

| Pair | Label |
|------|-------|
| (loves, man) | 1 ✅ |
| (loves, banana) | 0 ❌ |
| (loves, the) | 0 ❌ |
| (loves, car) | 0 ❌ |
| ... | 0 ❌ |

Now instead of updating $|V|$ weights, you update only $k+1$ weights per training step — a massive reduction for large vocabularies.

### The Objective Function

The training objective becomes: maximize the probability of positive pairs while minimizing the probability of negative pairs:

$$
J = \log \sigma(u_o^\top v_c) + \sum_{i=1}^{k} \mathbb{E}_{w_i \sim P_n(w)}\left[\log \sigma(-u_{w_i}^\top v_c)\right]
$$

Where:
- $\sigma(x) = \frac{1}{1+e^{-x}}$ is the sigmoid function
- The first term: *push the dot product of a real pair high* (so sigmoid → 1)
- The second term: *push the dot product of fake pairs low* (so sigmoid(-x) → 1 when x is very negative)

The model simultaneously pulls real context words **closer** in vector space and pushes negative samples **further away**.

### How Negative Samples Are Chosen

Negative words are **not** sampled uniformly at random. Instead, they are sampled from a **smoothed unigram distribution**:

$$
P(w_i) = \frac{f(w_i)^{3/4}}{\displaystyle\sum_{j=1}^{|V|} f(w_j)^{3/4}}
$$

Where $f(w)$ is the raw frequency of word $w$ in the corpus. The exponent $\frac{3}{4}$ compresses the frequency distribution — frequent words are still sampled more often, but rare words get a larger share than raw frequency would give them.

### How Many Negative Samples $k$?

- $k = 5\text{–}20$ for **small datasets**
- $k = 2\text{–}5$ for **large datasets**

More negative samples = more accurate but slower. For most practical purposes, $k=5$ is the sweet spot.

***

## 3.7 Hierarchical Softmax

This is an **alternative** to negative sampling — both solve the same bottleneck (full softmax is too slow) but via completely different mechanisms.

### The Binary Tree Structure

Hierarchical softmax organizes all $|V|$ words as the **leaf nodes** of a binary tree. Each internal node has a small vector of its own. To compute the probability of any word, instead of summing over all $|V|$ words, you **walk down the tree** from the root to that word's leaf, making a binary left/right decision at each node.

For a balanced tree of $|V|$ leaves, the path length from root to any leaf is $\log_2 |V|$ steps. This reduces computation from $O(|V|)$ to $O(\log |V|)$ per training step.

### Huffman Coding: Smarter Tree Construction

Rather than a balanced binary tree, Word2Vec builds a **Huffman tree** — where frequent words get shorter paths (fewer steps to the leaf) and rare words get longer paths.

- "the" (appears billions of times): path length = small
- Rare words: path length = larger

Since frequent words are updated far more often in training, giving them shorter paths means each update is cheaper.

### Probability Computation

At each internal node $n$ on the path to word $w$, a sigmoid decides whether to go left or right:

$$
P(\text{left} \mid n, v_c) = \sigma(v_c^\top \theta_n)
$$

The probability of word $w$ given center word $c$ is the product of all binary decisions along the path:

$$
P(w \mid c) = \prod_{n \in \text{path}(w)} \sigma\left(\llbracket n \to \text{left}\rrbracket \cdot v_c^\top \theta_n\right)
$$

Only the nodes along this path need to be updated per training step.

### Negative Sampling vs. Hierarchical Softmax

| | **Negative Sampling** | **Hierarchical Softmax** |
|---|---|---|
| Mechanism | Binary classification on pairs | Binary tree path probability |
| Complexity per step | $O(k)$ | $O(\log |V|)$ |
| Better for | Large corpora | Rare words |
| Used more in practice? | Yes | Less common |

***

## 3.8 The Geometry of Word Vectors

### What Training Actually Produces

After training, each word's embedding $v_w$ is a dense $d$-dimensional vector. The key property that emerges is that **semantic relationships are encoded as geometric directions** in this vector space.

### Measuring Similarity: Cosine Distance

Raw Euclidean distance is not the right similarity measure for word vectors, because it's sensitive to vector magnitude. The correct measure is **cosine similarity**, which measures only the angle between vectors:

$$
\text{cos}(v_a, v_b) = \frac{v_a \cdot v_b}{||v_a|| \cdot ||v_b||}
$$

- $+1$: very similar  
- $0$: unrelated  
- $-1$: opposite  

### The Analogy Property

A famous emergent property:

$$
\vec{\text{king}} - \vec{\text{man}} + \vec{\text{woman}} \approx \vec{\text{queen}}
$$

This works because relationships (like gender) become **directions in vector space**.

Examples:

| Analogy | Vector Operation |
|---|---|
| king → queen | $\vec{\text{king}} - \vec{\text{man}} + \vec{\text{woman}}$ |
| Paris → France | $\vec{\text{Paris}} - \vec{\text{France}} + \vec{\text{Italy}} \approx \vec{\text{Rome}}$ |
| walking → walked | $\vec{\text{walking}} - \vec{\text{walk}} + \vec{\text{swim}} \approx \vec{\text{swimming}}$ |

### One Important Caveat

These analogies are not perfect — they depend heavily on the training corpus and typically give approximate, not exact, matches.

***

## The Full Word2Vec Picture

```
Corpus → Slide window → (center, context) training pairs
                  ↓
     Skip-gram or CBOW neural network
                  ↓
    Full Softmax (slow for large |V|)
                  ↓
    Two solutions:
    ┌─────────────────────┬────────────────────────┐
    │  Negative Sampling  │  Hierarchical Softmax  │
    │  O(k) per step      │  O(log|V|) per step    │
    └─────────────────────┴────────────────────────┘
                  ↓
    Learned embeddings (matrix W)
                  ↓
    Geometry: similarity, analogies
```

The result is a dense $d$-dimensional vector for every word, trained purely from raw text, that captures meaning, syntax, and relationships — at a scale that no count-based method could match.