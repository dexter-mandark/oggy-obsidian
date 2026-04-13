---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

Before my model can process language, I need to encode it into numbers not text. The core question is: _how do I encode the meaning of a word mathematically?_
A single meaning to word mapping would not work, the word "bank" near "river" is very different from "bank" near "money," so a good representation must capture context and semantics, not just identity.

## Old-School Methods

#### Using Existing Thesauri & Ontologies (WordNet)

Before any statistical method existed, the most natural approach was: *why not just use human knowledge of language?* Linguists had already spent decades cataloguing how words relate to each other. Resources like **WordNet** (built at Princeton) encode these relationships explicitly as a structured graph.

WordNet organizes words into **synsets**, groups of words that share the same sense/meaning and connects them through semantic relations:

| Relation               | Meaning                  | Example              |
| ---------------------- | ------------------------ | -------------------- |
| **Synonymy**           | Same meaning             | "car" ↔ "automobile" |
| **Hypernymy** (is-a)   | One is a type of another | "dog" → "animal"     |
| **Hyponymy**           | Reverse is-a             | "poodle" → "dog"     |
| **Meronymy** (part-of) | One is a part of another | "wheel" → "car"      |
| **Antonymy**           | Opposite meaning         | "hot" ↔ "cold"       |
WordNet directly addresses the synonymy gap that purely statistical methods miss. In a raw word count model, "physician" and "doctor" are completely unrelated tokens. In WordNet, they live in the **same synset**, the model immediately knows they are equivalent. I can also compute **semantic similarity** from graph distance: "dog" and "cat" share a nearby common ancestor (mammal), while "dog" and "airplane" are far apart giving a numeric similarity score without any training data.

###### Doesn't Work

WordNet is fundamentally **static and human-curated**, which creates hard limits:
- It can't handle **new words** (slang, technical jargon, domain-specific terms) adding them requires manual effort
- Most words have **multiple senses** (WordNet lists 10+ senses for "bank") without context, it can't tell me which one applies
- It captures **categorical relations** (is-a, part-of) but not **graded similarity** it can't tell me "ecstatic" is more intense than "pleased"
- It is primarily English-centric, with much weaker coverage for other languages
  
A linguistic approach wont work, what I need is purely data-driven methods that learn about word meaning automatically from large text corpora. *Raw numbers baby!*

#### One-Hot Encoding

The simplest approach assigns each word a unique index in a vocabulary of size $V$ and represents it as a vector of all zeros except a single 1 at that index. Each word is assigned a unique index from $0$ to $V-1$. If my vocabulary has 50,000 words, every word becomes a 50,000-dimensional vector. This is called a **sparse representation**, the information density is nearly zero.

The problems are quite obvious:
- Every pair of word vectors has a dot product of zero. Two different words will **never share a non-zero position**, meaning the model sees _no similarity_ between "cat" and "kitten".
- The dimensionality is enormous and computationally wasteful.
- It carries **no semantic information** whatsoever.

#### Bag of Words (BoW)

BoW **extends one-hot to the document level**. Instead of representing individual words, it represents entire documents as vectors of word counts. I take the vocabulary, and for each document, count how many times each vocabulary word appears in it.
>[!example]
>
> **Corpus** for two documents:
> - $D1$: `the cat sat on the mat`
> - $D2$: `the cat ate the rat`
>
| | the | cat | sat | on | mat | ate | rat |
|---|---|---|---|---|---|---|---|
| **D1** | 2 | 1 | 1 | 1 | 1 | 0 | 0 |
| **D2** | 2 | 1 | 0 | 0 | 0 | 1 | 1 |


Each **row is the vector representation of a document not a word**. There are a few ways of doing this:
- **Raw Term Frequency**: Standard BoW, just count the raw occurrence of each word per document.
- **Binary BoW**: Replace counts with 0/1, just tracks presence or absence
- **TF (Term Frequency)**: Normalizes raw counts by the total number of words in the document so long documents don't dominate.$$\text{TF}(w, d) = \frac{\text{count of word } w \text{ in document } d }{\text{total words in document } d}$$
- **TF-IDF (Term Frequency, Inverse Document Frequency)**: Down weights words that appear in almost every document ("the", "is", "a") and up-weights rare, informative words. This is the most widely used variant in practice. For a corpus of $N$ total documents where $\text{df}(w)$ is the number of documents containing the word $w$, the TF-IDF is given as:$$
\text{TF-IDF}(w, d) = \text{TF}(w, d) \times \log \frac{N}{\text{df}(w)}
$$ Where the second log term is called the IDF.
###### Ah, still shitty

For **document-level classification tasks** (spam detection, topic labeling, sentiment analysis) BoW works well. If a document contains "profit," "shares," and "investor" many times, a classifier can correctly label it as financial news without understanding a single sentence.

But it ultimately fails:
- **Order blindness**: "Man bites dog" and "Dog bites man" produce identical BoW vectors, the model literally cannot distinguish them. Negation like "not good" looks the same as "good"
- **No word-level semantics**: "car" and "automobile" are completely unrelated columns, the model can't know they mean the same thing.
- **Sparsity**: A vocabulary of 100,000 words means every document vector has 100,000 dimensions, almost all zero.

#### Count-Based / Co-occurrence Matrices

The foundational idea is, **"You shall know a word by the company it keeps."** This is the **Distributional Hypothesis**, words that appear in similar linguistic contexts tend to have similar meanings. For example, "doctor" and "physician" will both frequently appear near words like "patient," "hospital," and "treatment," even if they never appear *next to each other directly*.


*I count how often words appear near each other across a large corpus*. For a corpus with vocabulary size $V$, I build a $V \times V$ matrix where entry $(i,j)$ records how many times word $i$ appears within a context window of word $j$. Each row is a word's vector and words with similar rows are semantically similar.

Given a corpus and a **context window** of size $k$ (typically 2–5 words), I slide the window across every word and count neighbors, for window of size $2$, at the $i$-th position the neighbours would be $i-2, i-1, i+1, i+2$.

>[!example] Building co-occurrence matrix
>**Corpus**: `the cat sat on the mat the cat ate the rat`(window = 2)
>
|       | the | cat | sat | on | mat | ate | rat |
|-------|-----|-----|-----|----|-----|-----|-----|
| **the** | 0 | 4 | 1 | 1 | 1 | 1 | 1 |
| **cat** | 4 | 0 | 2 | 0 | 0 | 1 | 0 |
| **sat** | 1 | 2 | 0 | 1 | 0 | 0 | 0 |
>The words "cat" and "rat" both appear near "the" and "ate", their rows will look similar, encoding their shared semantic category (animals).

###### Fixing Frequency Bias: PPMI

Raw frequencies are noisy. The word "the" co-occurs with almost everything, so its counts dominate. Two fixes are standard:
- **TF-IDF weighting**: Down-weights words that appear everywhere (low discriminatory power)
- **PMI (Point-wise Mutual Information)**: Measures whether two words co-occur *more than chance* would predict:
$$
\text{PMI}(w, c) = \log \frac{P(w, c)}{P(w) \cdot P(c)}
$$
	- If $P(w,c) \gg P(w) \cdot P(c)$: the words co-occur far more than random chance → strong positive signal → high PMI
	- If they co-occur at exactly the rate chance would predict → PMI = 0
	- If they co-occur less than chance → negative PMI

A high PMI for ("doctor", "stethoscope") means they co-occur far more than random chance, strong evidence of a real semantic relationship. In practice, **Positive PMI (PPMI)** is used, which clamps negative PMI values to 0.


###### Dimensionality Reduction: SVD

A vocabulary of 100,000 words produces a $100{,}000 \times 100{,}000$ co-occurrence or PPMI matrix with 10 billion entries, most of which are zero. This is unworkable for downstream tasks. I *need to compress it into a smaller, dense matrix* while preserving semantic structure.

>[!cite] Singular Value Decomposition
> Singular Value Decomposition factorizes any matrix $M$ into three matrices:
> $$
> M = U \Sigma V^\top
> $$
> 
> - $U$: word vectors $(V \times r)$, each row is a word's new representation
> - $\Sigma$: diagonal matrix of **singular values**, sorted from largest to smallest, each representing how much semantic variance that dimension captures.
> - $V^\top$: context vectors $(r \times V)$
> 
> By keeping only the **top $k$ singular values** (say $k = 300$) and discarding the rest, I get **Truncated SVD**:
> 
> $$
> M_k = U_k \Sigma_k V_k^\top
> $$
> 
> This is the mathematically optimal rank-$k$ approximation of $M$, I've kept the most important $k$ semantic dimensions and thrown away noise. Every word is now a dense 300-dimensional vector instead of a 100,000-dimensional sparse one.

After applying SVD, the reduced vectors capture **latent topics**.  Even if "car" and "automobile" never directly co-occur, they appear in similar documents, so their SVD-reduced vectors will be close in the compressed space.

But obviously it ain't enough:
- **Ordering of word is not considered.**
- **Computationally expensive**: Full SVD is $O(V^3)$ in the worst case, infeasible for millions of words
- **Not updatable**: Adding new documents or words requires recomputing SVD from scratch.
- **No subword knowledge**: Like all previous methods, it treats each word as atomic: "run," "running," and "ran" are completely separate entries
- **Cannot reach web scale**: Training on billions of tokens by scrapping the web is simply not feasible with matrix methods.

## word2vec

 Both **manual** (WordNet) or **count-based** (BoW, co-occurrence, SVD) methods aren't enough. None of the can capture the semantic similarities completely, and all of them are non-scalable.
 That's where prediction-based methods come in. Instead of counting and compressing, I *train a neural network to directly learn word vectors by predicting context*, which scales efficiently to any corpus size.


word2vec trains a **shallow neural network** (just one hidden layer) on a fake/proxy task: either "*predict context from a word (skip-gram)*" or "*predict a word from its context (CBoW)"*.

![[cbow-skipgram-architecture.png#invert_B]]

The model never actually uses the output of this task. What it keeps are the **learned hidden layer weights**, these become the word embeddings.

This is a classic example of **representation learning**: I design a task that forces the network to encode semantic meaning into its weights, then discard the task itself and keep only the weights.

###### Word Analogies

After training, word vectors encode a remarkable property. **Vector arithmetic captures analogical relationships**:
$$
\vec{v}_{\text{king}} - \vec{v}_{\text{man}} + \vec{v}_{\text{woman}} \approx \vec{v}_{\text{queen}}
$$
This is not engineered, it emerges from training. Because "king" and "queen" appear in similar contexts (royalty, palace, throne) but differ systematically in the gender direction (man vs. woman), their difference in embedding space captures that gender offset. Subtracting the male direction and adding the female direction navigates to "queen".
Other examples that work:
- Paris − France + Italy ≈ Rome  
- walking − walked + swam ≈ swimming  
- uncle − man + woman ≈ aunt
The geometry of the embedding space is not random, **directions encode relationships**, and those directions are consistent across the vocabulary.


## Skip-Gram and CBoW

Notation:
- $t$ = the **target word** (the word in the center of the window)
- $c$ = a **context word** (a neighbor in the window)
- $V$ = vocabulary size
- $d$ = embedding dimension (100-300, in practice)

Corpus: `the cat sat on the mat`, $V = 5$ is the vocabulary size, Embedding dimension let it be $d=3$, and context window be $k=2$ (look 2 words left and right of the target).

###### The Setup

Before any neural network, every word is converted to a $V$-dimensional **one-hot vector**, a row of all zeros except a single 1 at the word's index position.
- For "cat" (index 1): $x_{cat} = [0, 1, 0, 0, 0]^T$
- For "sat": $x_{sat} = [0, 0, 1, 0, 0]^T$
These are the raw inputs to the neural network. They carry no semantic information yet, just an identity tag.

Every word plays two completely different roles during training: it can be a target word at one step, and a context word at another step. The model tracks these two roles separately, using two weight matrices.
word2vec has **two weight matrices**, where every word as one row, both *initialized randomly* at the start of training:
- **Input/Embedding matrix** $W$: a ${V \times d}$ dimensional matrix. $$
W =
\begin{bmatrix}
0.1 & 0.6 & 0.3 \\
0.9 & 0.3 & 0.1 \\
0.1 & 0.8 & 0.2 \\
0.5 & 0.2 & 0.7 \\
0.3 & 0.6 & 0.4
\end{bmatrix}
\begin{array}{l}
\leftarrow \text{the (row 0)} \\
\leftarrow \text{cat (row 1)} \\
\leftarrow \text{sat (row 2)} \\
\leftarrow \text{on (row 3)} \\
\leftarrow \text{mat (row 4)}
\end{array}
$$
- **Output Matrix** $W'$: also a $V \times d$ dimensional matrix. $$
W' =
\begin{bmatrix}
0.4 & 0.2 & 0.6 \\
0.2 & 0.5 & 0.8 \\
0.7 & 0.1 & 0.6 \\
0.4 & 0.9 & 0.3 \\
0.6 & 0.2 & 0.5
\end{bmatrix}
\begin{array}{l}
\leftarrow \text{the (row 0)}: u_{w_0} \\
\leftarrow \text{cat (row 1)}: u_{w_1} \\
\leftarrow \text{sat (row 2)} \\
\leftarrow \text{on (row 3)} \\
\leftarrow \text{mat (row 4)}
\end{array}
$$

Multiplying a one-hot vector by $W$ *simply selects one row.* The result is a **$d$-dimensional embedding vector.**

$$
\begin{align} x_{cat} \times W &= v_{cat} \\ x_{\text{cat}} \times W &= [0, 1, 0, 0, 0]
\times
\begin{bmatrix}
0.1 & 0.6 & 0.3 \\
\mathbf{0.9} & \mathbf{0.3} & \mathbf{0.1} \\
0.1 & 0.8 & 0.2 \\
0.5 & 0.2 & 0.7 \\
0.3 & 0.6 & 0.4
\end{bmatrix} =  [0.9,\ 0.3,\ 0.1] = v_{cat} \end{align}
$$

#### Skip-Gram

**Given the target word $t$, predict the context words $c$ around it within a window of size $k$.**

```
Input (one-hot, 1×V)
        ↓  [W: V×d]
Hidden layer (d-dimensional, no activation function)
        ↓  [W': V×d → used as (W')ᵀ: d×V]
Output (softmax over all V words)
```

**Input**: the target word $w_t$ is fed in as a one-hot vector $x_t$ ($V$-dimensional). Multiplying by $W$ simply selects the corresponding row, this is the word's embedding vector $v_{t}$ (a $d$-dimensional vector).

**Hidden layer**: the embedding $v_t$ passes through. There is *no non-linear activation* here, the hidden layer is purely linear. This keeps the model fast and the embeddings in a simple geometric space.

**Output**: multiply $v_t$ by $(W')^T$ to get scores for every vocabulary word, then *apply soft-max to convert scores to probabilities*.

Target $t$ = "sat" (index 2), window = 2, Context words: "the", "cat", "on", "mat".

$$\begin{align} x_t &= [0,\ 0,\ 1,\ 0,\ 0] \\ \\ v_t &= x_t \times W = [0.1,\ 0.8,\ 0.2] \\ \\ \text{scores} = v_t \times (W')^T &=  [0.32,\ 0.58,\ 0.27,\ 0.82,\ 0.32]\end{align}$$
Multiplying the **target vector** $v_t$ by $(W')^T$ is equivalent to computing the dot product of $v_t$ with every row of $W'$ simultaneously:   
$$  
\text{score for word } i = v_t \cdot u_{w_i}
$$
Where $u_{w_i}$ is row $i$ of $W'$, the **context vector** of word $i$.  The score for the word "on" is: $\text{score}_{\text{on}} = [0.1,0.8,0.2]\cdot[0.4,0.9,0.3] = 0.82$

Using these scores, the probability of a context word $c$ given target word $t$ is computed via **softmax**:
$$
\begin{align} P(c \mid t) = \text{softmax}(\text{scores}) = \frac{\exp({\text{score}_c})}{\sum_{w \in V} \exp({\text{score}_w})} \\\\ =\frac{\exp(u_c^\top v_t)}{\sum_{w \in V} \exp(u_w^\top v_t)} \end{align}
$$

The numerator is the score of the specific context word $c$, exponentiated. And the denominator is sum of scores of every word in the vocabulary, exponentiated. These scores are just dot products between the context vectors and the target vectors: high dot product = high similarity = high probability.

$$\large  
\underbrace{x_t}_{(1 \times V)}
\xrightarrow{\times W_{(V \times d)}}
\underbrace{v_t}_{(1 \times d)}
\xrightarrow{\times (W')^T_{(d \times V)}}
\underbrace{\text{scores}}_{(1 \times V)}
\xrightarrow{\text{softmax}}
\underbrace{P(c \mid t)}_{(1 \times V)}
$$


For a corpus of $T$ words and context window $k$, *maximize the log-likelihood* of observing the actual context words:
$$
J(\theta) = \frac{1}{T} \sum_{t=1}^{T} \sum_{\substack{-k \ \leq j \ \leq \ k}} \log P(w_{t+j} \mid w_t)
$$
Where $P(w_{t+j} \mid w_t)$ refers to the probability (soft-max of scores) of a context word $w_{t+j}$ given target word $w_t$.

*The model is trained once per context word,* for the target word `sat` and for the
context word `on`, the maximization step updates:
- $\text{row}2$ of $W$ ($v_{\text{sat}}$): push `sat`'s target vector $v_{sat}$ closer to `on`'s context vector $u_{on}$.
- $\text{row}3$ of $W'$($u_{\text{on}}$): push `on`'s context vector closer to `sat`'s target vector
  
*This repeats for every (target, context) pair across the entire corpus.*

###### The Two Vectors

For each word, which vector is used depends on which role it plays in the current training pair:

| Word's role                | Which matrix is used | What the vector is called   |
| -------------------------- | -------------------- | --------------------------- |
| Word is the target $t$     | Matrix $W$           | $v_t$ is the target vector  |
| Word is a context word $c$ | Matrix $W'$          | $u_c$ is the context vector |

In one training step "sat" is the target → use $v_{\text{sat}}$ from $W$.
In a different training step, if "sat" appears as the context of "cat" → use $u_{\text{sat}}$ from $W'$.

#### CBOW

**Given the context words $c_1, c_2, ..., c_{2k}$ predict the target word $t$ in the middle.**

```
Input (2k one-hots, each 1×V)
        ↓  [W: V×d] — look up each context word separately
Hidden layer (average of context embeddings, d-dimensional, no activation function)
        ↓  [W': V×d → used as (W')ᵀ: d×V]
Output (softmax over all V words)
```

**Input**: Each context word $c_i$ is fed in as its own one-hot vector $x_{c_i}$ ($V$-dimensional). Multiplying each by $W$ selects the corresponding row, these are the context word embeddings $v_{c_i}$, each a $d$-dimensional vector.

**Hidden layer**: *all context embeddings are averaged into a single vector* $\hat{v}$. There is no non-linear activation.The averaging discards word order within the context window (hence "bag" of words) and produces a single vector representing the combined context.

**Output**: multiply $\hat{v}$ by $(W')^T$ to get scores for every vocabulary word, then apply softmax to convert scores to probabilities.

Target $t$ = "sat" (index 2), window = 2. Context words: "the" (0), "cat" (1), "on" (3), "mat" (4).

Hot-one encoding for all context words:
$$ \begin{align} x_{\text{the}} = [1,\ 0,\ 0,\ 0,\ 0] \\ x_{\text{cat}} = [0,\ 1,\ 0,\ 0,\ 0] \\ x_{\text{on}} = [0,\ 0,\ 0,\ 1,\ 0] \\ x_{\text{mat}} = [0,\ 0,\ 0,\ 0,\ 1]\end{align}
$$
Look up each in $W$ to get context embeddings:
$$
\begin{align} v_{\text{the}} = x_{\text{the}} \times W = [0.1,\ 0.6,\ 0.3] \\ v_{\text{cat}} = x_{\text{cat}} \times W = [0.9,\ 0.3,\ 0.1] \\ v_{\text{on}} = x_{\text{on}} \times W = [0.5,\ 0.2,\ 0.7]\\ v_{\text{mat}} = x_{\text{mat}} \times W = [0.3,\ 0.6,\ 0.4]\end{align}
$$
Average the context embeddings to form the hidden layer $\hat{v}$:
$$
\hat{v} =
\frac{v_{\text{the}} + v_{\text{cat}} + v_{\text{on}} + v_{\text{mat}}}{4} = [0.45,\ 0.425,\ 0.375]
$$
This single $d$-dimensional vector *$\hat{v}$ represents the combined meaning of all four context words*. It tells the network: the missing word sits in a context that collectively feels like this.

Compute scores via $(W')^T$:
$$
\text{scores} = \hat{v} \times (W')^T
$$

Multiplying $\hat{v}$ by $(W')^T$ is equivalent to computing the dot product of $\hat{v}$ with every row of $W'$ simultaneously:
$$
\text{score for word } i = \hat{v} \cdot u_{w_i}
$$

where $u_{w_i}$ is row $i$ of $W'$, the target vector of word $i$.The score for "sat" is: $\text{score}_{\text{sat}} =[0.45,\ 0.425,\ 0.375] \cdot [0.7,\ 0.1,\ 0.6] =  0.583$

Now I can use softmax to compute probabilities:
$$
\begin{align} P(t \mid c_1, ..., c_{2k}) &=
\text{softmax}(\text{scores}) =
\frac{\exp(\text{score}_t)}{\sum_{w \in V} \exp(\text{score}_w)} \\ \\ &= \frac{\exp(u_t^\top \hat{v})}{\sum_{w \in V} \exp(u_w^\top \hat{v})} \end{align}
$$


The numerator is the score of the specific target word $t$, exponentiated. The denominator is the sum of scores of every word in the vocabulary, exponentiated. High dot product between $\hat{v}$ and $u_t$ = the averaged context is geometrically close to that target word = high probability.

$$
\large
\begin{align} \underbrace{x_{c_i}}_{(1 \times V)}\ \text{(×2k)}
\rightarrow & \times W_{(V \times d)}
\rightarrow \underbrace{v_{c_i}}_{(1 \times d)}\ \text{(×2k)}
\rightarrow \text{average}
\rightarrow \underbrace{\hat{v}}_{(1 \times d)} \\
\rightarrow & \times (W')^T_{(d \times V)} 
\rightarrow \underbrace{\text{scores}}_{(1 \times V)}
\rightarrow \text{softmax}
\rightarrow \underbrace{P(t \mid \text{ctx})}_{(1 \times V)} \end{align}
$$


For a corpus of $T$ words and context window $k$, maximize the log-likelihood of observing the actual target word:
$$
J(\theta) =
\frac{1}{T}
\sum_{t=1}^{T}
\log P\bigl(w_t \mid w_{t-k}, ..., w_{t-1}, w_{t+1}, ..., w_{t+k}\bigr)
$$
where $P(w_t \mid \text{context})$ is the softmax probability of the true target word given all context words.

The model makes one prediction per window. For target `sat` given context {"the", "cat", "on", "mat"}, the update step:
- Rows 0, 1, 3, 4 of $W$ ($v_{\text{the}}, v_{\text{cat}}, v_{\text{on}}, v_{\text{mat}}$): push all four context vectors toward `sat`'s target vector $u_{\text{sat}}$
- Row 2 of $W'$ ($u_{\text{sat}}$): push `sat`'s target vector closer to the averaged context $\hat{v}$

This repeats for every window position across the entire corpus.

###### The Two Vectors

| Word's role | Which matrix | What the vector is called |
|---|---|---|
| Word is a context word $c$ | Matrix $W$ | $v_c$ — the context vector |
| Word is the target $t$ | Matrix $W'$ | $u_t$ — the target vector |

In one training step, "sat" is the target → use $u_{\text{sat}}$ from $W'$.
In a different training step, if "sat" appears as a context word around some other target → use $v_{\text{sat}}$ from $W$.

## Negative Sampling

For the probability calculation:
$$\begin{align} P(c \mid t) =  \frac{\exp({\text{score}_c})}{\sum_{w \in V} \exp({\text{score}_w})} =\frac{\exp(u_c^\top v_t)}{\sum_{w \in V} \exp(u_w^\top v_t)} \end{align}$$

For the denominator, I need to compute a dot product between $v_t$ and every single row of $W'$, all $V$ of them, at every single training step.
With $V=1,000,000$ words and billions of training steps, this is computationally impossible.  *Softmax just wont work, it's top slow for a large corpora.*

I can re frame the skip-gram problem. Instead of asking *"what is the context word?"*, I ask a much simpler binary question: **Is candidate word $c$ a real neighbor of target word $t$, or not?**

This turns a massive multi-class classification problem (one class per vocabulary word) into a simple **binary classification** which is far cheaper to train.

#### Skip-gram with Negative Sampling (SGNS)

###### Making Positive and Negative Examples

Take the corpus and slide a context window of size $k$ over every word. Every **(target word $t$, context word $c$)** pair that actually co-occurs in the window is a **positive example** with label $1$. This is repeated for **every single word** in the corpus, generating millions of positive $(t, c)$ pairs.


For each positive pair $(t, c)$, randomly sample $k$ words from the lexicon (vocabulary) words that do not appear in $t$'s context window. These are **negative examples** with label $0$.
These negative examples  are **not sampled uniformly**. Instead, they are sampled from a **smoothed unigram distribution**:
$$
P(w_i) = \frac{f(w_i)^{3/4}}{\displaystyle\sum_{w \in \mathcal{V}} f(w)^{3/4}}
$$

Where $f(w)$ is the raw frequency of word $w$ in the corpus. The exponent $\frac{3}{4}$ compresses the frequency distribution, without it, common words like "the" and "a" would dominate, they'd be sampled as negatives almost every time. Rare words would almost never appear.
The $\frac{3}{4}​$ exponent smooths the distribution: *it reduces the weight of very frequent words and slightly boosts rare ones*, giving the model a better chance to learn representations for all vocabulary words, not just the common ones.

More negative samples = more accurate but slower. For most practical purposes, $k=5$ is the sweet spot.

###### The Setup

Similar to the multi-classification approach, before any training begins, the model creates **two randomized matrices**, where every word appears as a row.
- **Matrix $W$**:  $V \times d$ matrix. When a word acts as the **target word $t$**, I pull its row from this matrix. That row is its **target vector $v_t$**.

**Matrix $W'$**: $V \times d$ matrix. When a word plays the role of a **context word $c$**, I look up its row here. That row is its **context vector** $u_c$.

Training adjusts every number in both matrices until they encode semantic meaning.

###### Making a Prediction

For **every training pair $(t, c)$**, exactly two rows are retrieved, one from each matrix.

Example: For (sat, on) the label = 1, for (sat, piano), label = 0
$$
\begin{align} v_{\text{sat}} &= W = [0.1,\ 0.8,\ 0.2] \quad \leftarrow \text{row 2 of } W \\ u_{\text{on}} &= W' = [0.4,\ 0.9,\ 0.3] \quad \leftarrow \text{row 3 of } W' \\ u_{\text{piano}} &= W'[\text{idx}] = [0.6,\ 0.1,\ 0.5] \quad \leftarrow \text{piano's row from } W' \end{align}
$$

For a pair $(t, c)$ I predict: *is $c$ a real neighbor of $t$?*
$$
P(+1 \mid t,\ c) = \sigma(u_c^\top v_t) = \frac{1}{1 + e^{-(u_c^\top v_t)}}
$$

If both vectors $u_c, v_t$ point in a **similar direction** (high dot product), then $\sigma$ returns a value near 1 ->  model says "yes, they are neighbors." If their dot product is low or negative, $\sigma$ returns near 0 → model says "no, not neighbors".

For (sat, on) with label =1:
$$\begin{align} u_{\text{on}}^\top v_{\text{sat}} &= [0.4,\ 0.9,\ 0.3] \cdot [0.1,\ 0.8,\ 0.2]
 \\ \\ P(+1 \mid \text{sat},\ \text{on}) &= \sigma(0.82) = \frac{1}{1+e^{-0.82}} = 0.688 \end{align}$$

True label = 1. Model says 68.8% chance it's real. Partially right, not confident enough. There is loss, so need to update the weights matrices.

###### Model Training

Training adjusts the vectors so that:
- **Real pairs**: the dot product becomes large → $\sigma$ pushes toward 1
- **Fake pairs**: the dot product becomes small/negative → $\sigma$ pushes toward 0

The loss for one positive pair and $k$ negative pairs using **binary cross-entropy**:
$$
\mathcal{L}(t,\ c,\ c'_1,...,c'_k) = -\log \sigma(u_c^\top v_t) - \sum_{i=1}^{k} \log \sigma(-u_{c'_i}^\top v_t)
$$

- $-\log \sigma(u_c^\top v_t)$: loss for the positive pair. $\sigma(u_c^\top v_t)$ should be high (close to 1). 
- $-\log \sigma(-u_{c'_i}^\top v_t)$: loss for each negative pair. For the sigmoid function  $\sigma(-u_{c'_i}^\top v_t)  = 1 - \sigma(u_{c'_i}^\top v_t)$, the probability that the pair is *fake*. I want this to be high (close to 1), i.e., I want the dot product of the fake pair to be large and negative.

Gradient descent can be used to compute how to nudge each vector to reduce the loss, and applies the update:
$$\begin{align} v_t \leftarrow v_t - \eta \cdot \frac{\partial \mathcal{L}}{\partial v_t} \\ u_c \leftarrow u_c - \eta \cdot \frac{\partial \mathcal{L}}{\partial u_c} \\ u_{c'_i} \leftarrow u_{c'_i} - \eta \cdot \frac{\partial \mathcal{L}}{\partial u_{c'_i}}\end{align}$$

- **For the positive pair** (sat, on): Pull $v_{\text{sat}}$ and $u_{\text{on}}$ **closer together**, their dot product needs to be higher  
- **For the negative pair** (sat, piano): Push $v_{\text{sat}}$ and $u_{\text{piano}}$ **further apart**, their dot product needs to be more negative  

**Critical efficiency point**: only $k + 1$ rows of $W'$ are touched per training step, the one real context word and $k$ negative samples. Compare this to full softmax, which touches all $V$ rows of $W'$ every step. With $k = 5$ and $V = 1{,}000{,}000$, negative sampling is 200,000× cheaper per update.


```
For every position t in the corpus:
│
├─ Form positive pairs: (t, c) for each real neighbor c in window
│   └─ Look up v_t from W,  u_c from W'
│
├─ Sample k negative words c' randomly
│   └─ Look up v_t from W,  u_{c'} from W'
│
├─ Compute dot products and sigmoid predictions
│
├─ Compute loss (binary cross-entropy)
│
└─ Update via gradient descent:
    ├─ Row t of W  → v_t  (nudged by ALL pairs: real + fake)
    ├─ Row c of W' → u_c  (nudged to be closer to v_t)
    └─ Row c' of W' → u_{c'} (nudged to be further from v_t)
```

###### Discard $W'$, Keep $W$

Once training converges, $W'$ has served its purpose, it provided the context vectors $u_c$ that acted as "anchors" during training, pulling and pushing $v_t$ into a meaningful geometry. I **discard $W'$** and keep only $W$. Each row of $W$ is the final word embedding for one word:
$$
\text{embedding of word } w = W[w] = v_w \quad (1 \times d)
$$
These are the vectors I use for everything: measuring similarity, word analogies, downstream tasks.

###### Nothing but Logistic Regression

This is exactly logistic regression, with one critical difference:

|                               | Standard Logistic Regression | Skip-gram Negative Sampling         |
| ----------------------------- | ---------------------------- | ----------------------------------- |
| **Input features**            | $x$ is fixed, never updated  | $v_t$ is a row of $W$, **learned**  |
| **Weights**                   | $\theta$ is learned          | $u_c$ is a row of $W'$, **learned** |
| **Prediction**                | $\sigma(\theta^\top x)$      | $\sigma(u_c^\top v_t)$              |
| # parameters updated per step | All of $\theta$              | Just 2–3 rows across both matrices  |

In standard logistic regression, the input $x$ is fixed data I give the model. In Skip-gram, the "input" $v_t$ is itself a parameter being learned. Both sides of the dot product are updated simultaneously. This is why two matrices are necessary, each word needs a separate learnable representation for each role it can play.

## Subsampling Frequent Words

Common words like "the", "a", "of", "is" create two distinct problems during training:

**Problem 1: Pairs with stop words are uninformative.**  
The pair ("fox", "the") teaches the model almost nothing about the meaning of "fox". "the" co-occurs with nearly every word in the language. It carries no discriminative signal, it tells me nothing about what "fox" means or which other words it's similar to. Yet the training loop generates enormous numbers of these useless pairs.

**Problem 2: Stop words are massively over-represented.**  
In any large corpus, "the" may appear 50 billion times. "fox" may appear 10 million times. The model doesn't need 50 billion training samples to learn a good vector for "the", it saturates long before that. All those extra samples just waste computation.

###### The Fix: Probabilistic Deletion

Before building any training pairs, every word in the corpus is passed through a filter. Each occurrence of a word $w$ is **randomly deleted from the training text** with probability:
$$
P(\text{delete } w) = 1 - \sqrt{\frac{t}{f(w)}}
$$

where $f(w)$ is the word's frequency as a fraction of the total corpus size, and $t$ is a threshold (typically $t = 10^{-5}$).
- If $f(w)$ is very large (extremely common word like "the"): the fraction $\frac{t}{f(w)}$ is tiny, its square root is tiny, so $P(\text{delete})$ is close to 1 → "the" gets deleted from most positions  
- If $f(w)$ is small (rare word like "apricot"): the fraction $\frac{t}{f(w)}$ is large, often $> 1$, so $P(\text{delete})$ is clamped to 0 → rare words are never deleted  

*This is applied before sliding the window*. So if "the" is deleted from position $i$, it simply disappears from the text at that position. The window slides over the gap as if "the" was never there.

For the original text `the fox jumped over the lazy dog`, after subsampling, "the" might be deleted at both positions: `fox jumped over lazy dog`. Now when the window is centered on "jumped", its context includes "fox", "over", "lazy", words that are actually semantically informative. 

This also makes **the effective context window larger in terms of content words**. Removing frequent words means context words that were previously 4–5 positions away are now within reach of the window. 

## fastText: Subword Embedding

###### Problems With word2vec

word2vec has some fundamental limitations, even with subsampling:

**Problem 1: Stop words have no special status.**
In `The cat sat on the mat`, "the" is just another word in the vocabulary. word2vec has no mechanism to treat it as a stopword vs. a content word. The model doesn't know whether "the" appearing near "cat" is a meaningful contextual signal or just grammatical noise.

**Problem 2: Unknown words (OOV problem).**
word2vec assigns embeddings only to words it saw during training. If a new word appears at test time, the model has no vector for it and cannot represent it at all. This is the **out-of-vocabulary** (OOV) problem, and it is a hard ceiling on word2vec's applicability.

*fastText directly addresses the OOV problem by going below the word level to character n-grams.*

###### The Solution

Instead of learning one vector for the whole word "where", fastText represents it as:
$$
\text{where} = \texttt{<wh},\ \texttt{whe},\ \texttt{her},\ \texttt{ere},\ \texttt{re>},\ \texttt{<where>}
$$
The `<` and `>` boundary markers distinguish the word-initial and word-final n-grams from those appearing in the middle of a word. With $n=3$, every word is decomposed into its constituent character trigrams plus the whole word itself.

Skip-gram is then run **on each n-gram independently**, the model learns a separate embedding for every n-gram that appears in training. The final embedding for a word is the **sum of all its constituent n-gram vectors**:
$$
v_{\text{where}} = v_{\texttt{<wh}} + v_{\texttt{whe}} + v_{\texttt{her}} + v_{\texttt{ere}} + v_{\texttt{re>}} + v_{\texttt{<where>}}
$$

Consider a completely new word like "whereabouts" that never appeared in training. word2vec gives up no vector. fastText decomposes it:
$$
\text{whereabouts} = \texttt{<wh},\ \texttt{whe},\ \texttt{her},\ \texttt{ere},\ \texttt{rea},\ \texttt{eab},\ \texttt{abo},\ \texttt{bou},\ \texttt{out},\ \texttt{uts},\ \texttt{ts>}
$$
Many of these n-grams $\texttt{<wh}$, $\texttt{whe}$, $\texttt{her}$, $\texttt{ere}$, were learned from "where", "whenever", "wherever", etc. Summing their vectors gives a reasonable embedding for "whereabouts" even though the word itself was never seen. The model generalizes to new words through **shared subword structure**.

###### Morphologically Rich Languages

This also helps with languages like German, Turkish, or Finnish where a single base word can generate hundreds of surface forms through inflection. "run", "running", "runner", "runs", "outrun" all share n-grams like $\texttt{run}$, $\texttt{unn}$. In word2vec, each is a completely independent vocabulary entry. In fastText, they share subword vectors and their embeddings are naturally related, the model learns morphological structure automatically.

## Count-Based vs. Prediction-Based
#### Count-Based Methods (TF-IDF, PMI, PPMI)

*Scan the entire corpus once, count how many times every pair of words co-appears within a window, and store those counts in a $V \times V$ matrix. That is the entire training process.*

###### Where it Shines

**Fast to train**
The computational work is a single pass over the corpus. I scan every document, slide a window, increment counts. Once the matrix is built, you apply TF-IDF or PMI weightings, simple arithmetic. There is no iterative optimization loop, no gradient descent, no repeated passes. Training cost scales only with corpus size, not with the number of training iterations.

**Faithfully Preserves Co-occurrence Statistics**
If words "ice" and "cold" appear together 14,000 times in your corpus, that count is stored exactly as 14,000. The raw statistical signal is preserved with full precision. Nothing is thrown away. This matters because co-occurrence frequency is genuinely informative, it is the empirical backbone of the Distributional Hypothesis.

###### Where it Fails

**Problem 1: Large, Sparse Matrix**
The matrix is $V \times V$. With a vocabulary of 100,000 words, that is 10 billion cells. The vast majority of those cells are zero,  most word pairs never co-occur. This matrix is unwieldy to store, and extracting dense embeddings from it is not straightforward.

**Problem 2: Disproportionate Weight to Frequent Pairs**
Stop words like "the", "a", "of" co-occur with virtually every word in the language thousands of times. The count matrix therefore has enormous numbers in rows and columns corresponding to stop words. These massive counts dominate any similarity measure derived from the matrix. The pair ("the", "cat") scores highly not because "the" and "cat" are semantically related, but simply because "the" appears everywhere. The model has no natural mechanism to suppress this noise, PMI helps somewhat, but the raw count problem does not fully disappear.

**Problem 3: Not Designed for Dense Embeddings**
The output of count-based methods is a very high-dimensional, very sparse vector for each word (one dimension per vocabulary word). This is the opposite of what downstream NLP tasks want, they need short, dense, information-packed vectors. Count-based methods are better thought of as similarity measures than as embedding generators.


#### Prediction-Based Methods (Word2Vec)

*Slide a context window across the corpus, generate (target, context) pairs, and iteratively run binary logistic regression to adjust two learned matrices until similar-context words have similar vector representations.*

###### Where it Shines

**Dense, Low-Dimensional Vectors**
The output is a $d$-dimensional vector per word, where $d$ is typically 100–300. Dense vectors work well as inputs to downstream classifiers, neural networks, and other NLP tasks. Because the vector space is compact, models trained on these vectors are less prone to overfitting, fewer parameters to memorize noise.

**Captures Complex Patterns**
*The embedding space encodes not just similarity but relational structure*:
$$
\vec{v}_{\text{king}} - \vec{v}_{\text{man}} + \vec{v}_{\text{woman}} \approx \vec{v}_{\text{queen}}
$$
Linear arithmetic in the vector space captures abstract analogical relationships gender, capital-country, verb tense. Count-based methods produce no such structure because they do not learn a vector space in the geometric sense they just store counts.

###### Where it Fails

**Scales With Corpus Size**
Suppose your vocabulary is fixed at $V$ words, the same set of words throughout.
- Count-based: adding more documents just increments existing cells in the $V \times V$ matrix. The matrix size doesn't change. Updating is O(new tokens), nothing more.  
- word2vec: every new token in the corpus generates new context windows. Every context window generates new (target, context) pairs. Every pair triggers a forward pass, a loss computation, and a gradient update. If I add 1 million new tokens to the corpus with a window of size 7, I'm adding approximately 7 million new training steps. *The computational cost grows linearly and strictly with corpus size.*

This means that if my corpus doubles, the training time roughly doubles. For internet-scale corpora (billions of words), this is a serious practical constraint.

**Does Not Use Co-occurrence Statistics Directly**
word2vec's training signal is binary, each pair is labeled 1 (real) or 0 (fake). The model never explicitly sees the number "ice and cold co-appeared 14,000 times". It only sees individual instances, one at a time. The raw count which is a statistically rich, precise signal, is discarded and replaced by the weaker signal of individual binary judgments. The model may encounter the pair (ice, cold) 14,000 times and update incrementally 14,000 times rather than ever processing the fact that the co-occurrence is 14,000 in one shot.



## GloVe


GloVe's central idea, is to **use the co-occurrence matrix as the training target rather than as the output**.
Instead of either:
- storing the raw counts directly (count-based), or  
- learning from individual word pairs one at a time (word2vec)

GloVe does something different: it builds the $V \times V$ co-occurrence matrix first, getting all the statistical precision of a count-based method and then trains a **neural objective that learns dense vectors $v_i$ and $v_j$ whose dot product predicts the log of the co-occurrence count**:
$$
v_i \cdot v_j \approx \log X_{ij}
$$

The co-occurrence matrix is scanned once (fast, like count-based). The training optimizes over entries of this matrix (not individual corpus tokens, so it doesn't re-scale with raw corpus size). The output is dense, geometric vectors (like word2vec) that still honor the global statistical structure of the corpus.

#### The Co-occurrence Ratio Intuition

Take two related words: **"ice"** and **"steam"**. The question GloVe starts with is: *how can I, purely from co-occurrence statistics, figure out what makes ice and steam different from each other, and what makes them similar?*

To answer this, take four **probe words**, these are words I use to interrogate the relationship between "ice" and "steam". Possible probe words: `solid, gas, water, fashion`, where fashion relates to neither.

I define $P(w_k \mid w_i)$ as the probability that probe word $w_k$ appears in the context of word $w_i$. This is just the count of how many times $w_k$ and $w_i$ co-occurred, divided by the total count of all words that co-occurred with $w_i$:
$$
P(w_k \mid w_i) = \frac{X_{ik}}{\sum_{j} X_{ij}}
$$
where $X_{ij}$ is the raw co-occurrence count between words $i$ and $j$.

From a sample corpus of 6-billion tokens, the actual values are:

| Probe word $w_k$ | $P(w_k \mid \text{ice})$ | $P(w_k \mid \text{steam})$ |
|---|---|---|
| solid | $1.9 \times 10^{-4}$ | $2.2 \times 10^{-5}$ |
| gas | $6.6 \times 10^{-5}$ | $7.8 \times 10^{-4}$ |
| water | $3.0 \times 10^{-3}$ | $2.2 \times 10^{-3}$ |
| fashion | $1.7 \times 10^{-5}$ | $1.8 \times 10^{-5}$ |

- solid: $P(\text{solid} \mid \text{ice}) = 1.9 \times 10^{-4}$. Is that high or low? Hard to say in isolation. 
- water: $P(\text{water} \mid \text{ice}) = 3.0 \times 10^{-3}$. This is higher but is it high because water is meaningfully related to ice, or just because "water" is a very common word in general?  
- fashion: $P(\text{fashion} \mid \text{ice}) = 1.7 \times 10^{-5}$. Very small but is this small because fashion is irrelevant, or because "fashion" is just a rare word?  

**The problem with raw probabilities**: they conflate two separate signals *how frequent the word is overall* and *how specific its relationship to "ice" is*. 

I can compute the ratio of co-occurrence probabilities:
$$
\text{ratio} = \frac{P(w_k \mid \text{ice})}{P(w_k \mid \text{steam})}
$$

| Probe word $w_k$ | $P(w_k \mid \text{ice})$ | $P(w_k \mid \text{steam})$ | Ratio |
| ---------------- | ------------------------ | -------------------------- | --------- |
| solid            | $1.9 \times 10^{-4}$     | $2.2 \times 10^{-5}$       | 8.9   |
| gas              | $6.6 \times 10^{-5}$     | $7.8 \times 10^{-4}$       | 0.085 |
| water            | $3.0 \times 10^{-3}$     | $2.2 \times 10^{-3}$       | 1.36  |
| fashion          | $1.7 \times 10^{-5}$     | $1.8 \times 10^{-5}$       | 0.96  |

The ratio $\frac{P(w_k \mid w_i)}{P(w_k \mid w_j)}$ tells me what I need, it encodes three types of relationships:

| Ratio value | Meaning                                                |
| ----------- | ------------------------------------------------------ |
| **>> 1**    | $w_k$ is specific to $w_i$ (ice-like, solid-like)      |
| **<< 1**    | $w_k$ is specific to $w_j$ (steam-like, gas-like)      |
| **≈ 1**     | $w_k$ is neutral equally related to both or to neither |

- Words that are common but non-discriminative (like "water") appear frequently in both numerator and denominator they divide to ≈ 1, contributing nothing.  
- Words that are rare but non-discriminative (like "fashion") are small in both numerator and denominator — they also divide to ≈ 1, contributing nothing.  
- **Only words that are differentially related to $w_i$ vs $w_j$** produce a ratio that is meaningfully above or below 1.  

*This is what raw probabilities cannot do*. Raw $P(\text{water} \mid \text{ice}) = 0.003$ is large and would pull "ice" toward "water" in any similarity measure. But in the ratio, water's equal association with both ice and steam is correctly neutralized. *The ratio isolates the distinguishing signal.*

This is information I want my word vectors to encode. The ratio $\frac{P_{ik}}{P_{jk}}$ depends on three words, $w_i$, $w_j$, and $w_k$. And captures the full relational geometry between them.
So GloVe's design principle becomes: **build a model whose word vectors, through some function $F$, produce exactly this ratio $\frac{P_{ik}}{P_{jk}}$**

#### GloVe Objective Function

The goal is a model where word vectors capture the ratio $\frac{P_{ik}}{P_{jk}}$. In the most general form, some function $F$ of the vectors for words $i$, $j$, and $k$ should equal this ratio:
$$
F(w_i,\ w_j,\ \tilde{w}_k) = \frac{P_{ik}}{P_{jk}}
$$
Where $w_i, w_j$ are the **target vectors** (from matrix $W$) and $\tilde{w}_k$ is the **context vector** (from matrix $W'$), the same two-matrix structure as word2vec.

Now the question is: what must $F$ look like? Three constraints nail it down completely.
###### Constraint 1: Vector Differences Encode Meaning
The ratio is about the **difference** between words $w_i$ and $w_j$ in relation to $w_k$. Vector spaces are inherently linear, so the natural way to express a difference between two words is through their vector difference. So restrict $F$ to only depend on $w_i - w_j$:
$$
F(w_i - w_j,\ \tilde{w}_k) = \frac{P_{ik}}{P_{jk}}
$$
###### Constraint 2: The Argument Must Be a Scalar
$F$ takes two vectors and returns a scalar (the ratio). To avoid $F$ mixing vector dimensions in messy ways, take the dot product first:
$$
F\!\left((w_i - w_j)^\top \tilde{w}_k\right) = \frac{P_{ik}}{P_{jk}}
$$
###### Constraint 3: Symmetry, Target and Context Are Interchangeable

In a word-word co-occurrence matrix, the distinction between "word" and "context word" is arbitrary. "cat" appearing in the context of "sat" is the same event as "sat" appearing in the context of "cat". So the model must be symmetric: swapping $w \leftrightarrow \tilde{w}$ should leave the model unchanged.

For this symmetry to hold, $F$ must be a **homomorphism** between addition and multiplication, meaning it must turn differences into ratios. The only continuous function with this property is the exponential. Applying this:

$$
\begin{align} \exp\!\left((w_i - w_j)^\top \tilde{w}_k\right) = \frac{P_{ik}}{P_{jk}} \\ \\ \frac{\exp(w_i^\top \tilde{w}_k)}{\exp(w_j^\top \tilde{w}_k)} = \frac{P_{ik}}{P_{jk}} \end{align}
$$
This means the individual terms must satisfy:
$$
\exp(w_i^\top \tilde{w}_k) = P_{ik} = \frac{X_{ik}}{X_i}
$$
Taking log of both sides:
$$
w_i^\top \tilde{w}_k = \log P_{ik} = \log X_{ik} - \log X_i
$$
###### Absorbing Row Sums into Bias Terms

For context word target word $i$, with target word $w_i$ and context word $k$ with context vector $\tilde{w}_k$, I can state: $w_i^\top \tilde{w}_k = \log P_{ik} = \log X_{ik} - \log X_i$
And since the model is symmetric, I can write the same equation with swapped roles, word $i$ as the context vector and word $k$ as the target:
$$w_k^\top \tilde{w}_i = \log X_{ki} - \log X_k$$
And as $X_{ik} = X_{ki}$ (co-occurrence is symmetric), I can add both the equations to get: 
$$
\begin{align} w_i^\top \tilde{w}_k + w_k^\top \tilde{w}_i = 2\log X_{ik} - \log X_i - \log X_k \\ \\ \frac{w_i^\top \tilde{w}_k + w_k^\top \tilde{w}_i}{2} = \log X_{ik} - \frac{1}{2}\log X_i - \frac{1}{2}\log X_k \end{align}
$$

The terms $\frac{1}{2}\log X_i$ and $\frac{1}{2}\log X_k$ are **word-specific constants**,  $\frac{1}{2}\log X_i$ depends only on word $i$ (its total co-occurrence count with everything), and $\frac{1}{2}\log X_k$ depends only on word $k$. They tell me nothing about the specific relationship between $i$ and $k$. *Rather than computing them from raw counts at every step, GloVe absorbs them into learnable scalar biases $b_i$ and $b_k$:*
$$
\frac{1}{2}\log X_i \approx b_i \qquad \frac{1}{2}\log X_k \approx b_k
$$
Substituting:
$$
\frac{w_i^\top \tilde{w}_k + w_k^\top \tilde{w}_i}{2} = \log X_{ik} - b_i - b_k
$$

Since both $w_i^\top \tilde{w}_k$ and $w_k^\top \tilde{w}_i$ are being trained to hit the same target $\log X_{ik}$, the model simplifies this into a single symmetric prediction equation:

$$
\boxed{w_i^\top \tilde{w}_k + b_i + b_k = \log X_{ik}}
$$

This is the **GloVe prediction equation**. The dot product of two word vectors, plus two scalar biases, should equal the log of their co-occurrence count. This is what the model tries to achieve for every pair $(i, k)$ in the vocabulary.

#### The Loss Function

Cast this as a least-squares problem, for every pair $(i, k)$ in the vocabulary, minimize the squared difference between what the model predicts and what the data says:
$$
\mathcal{L} = \sum_{i,k=1}^{V} \left(w_i^\top \tilde{w}_k + b_i + b_k - \log X_{ik}\right)^2
$$
But this has a couple problems:
- **Rare co-occurrences are noisy**: A pair that co-occurred only once or twice has very little statistical signal, it might be a fluke. Yet the unweighted loss gives it exactly as much training influence as a pair that co-occurred 50,000 times.
- **Frequent co-occurrences dominate**: Pairs involving very common words like "the" and "of" have enormous $X_{ik}$ values. Their large log counts make their squared errors large, and they dominate the gradient signal, drowning out the rarer but more semantically informative pairs.

###### The Weighting Function

So I introduce a weighting function $f(X_{ik})$ such that:
- $f(0) = 0$: pairs that never co-occurred should contribute zero to the loss.
- $f(X)$ must be **non-decreasing**: rare co-occurrences should get less weight than frequent ones. More evidence → more weight.
- $f(X)$ must be **relatively small for large $X$**:  very frequent co-occurrences should not be over-weighted. The function must level off so that "the" and "a" don't dominate everything.

The function that satisfies all three properties is a **piece-wise power function with a cutoff**:
$$
f(X) =
\begin{cases}
\left(\dfrac{X}{X_{\max}}\right)^\alpha & \text{if } X < X_{\max} \\[6pt]
1 & \text{otherwise}
\end{cases}
$$
This function works cause:
- $f(0) = 0$ 
- For $0 < X < X_{\max}$: $f(X)$ rises from 0 to 1 as a smooth power curve, rare pairs get small weight, frequent pairs get larger weight.  
- For $X \geq X_{\max}$: $f(x) = 1$: the weight is capped at 1. No pair, no matter how frequent, can have more influence than this ceiling  

The parameters $X_{\max}$ and $\alpha$ are set empirically. The original paper fixes $X_{\max} = 100$ and finds that $\alpha = \frac{3}{4}$ gives the best performance (the same fractional exponent used in word2vec's negative sampling distribution).

Finally the loss function becomes:
$$
\boxed{\mathcal{L} = \sum_{i,k=1}^{V} f(X_{ik})\left(w_i^\top \tilde{w}_k + b_i + b_k - \log X_{ik}\right)^2}
$$
The learnable parameters are: $w_i$ (rows of $W$), $\tilde{w}_k$ (rows of $W'$), $b_i$ (scalar bias for target word $i$), and $b_k$ (scalar bias for context word $k$). 
The co-occurrence matrix $X$ is computed once before training and held completely fixed. Minimize $\mathcal{L}$ using gradient descent.

#### Properties of GloVe

**Fast training**: the co-occurrence matrix is built with a single corpus scan. Training optimizes over the nonzero entries of $X$ which in practice is far smaller than $V^2$ because most word pairs never co-occur. This makes GloVe significantly faster than word2vec on the same corpus.

**Scalable to huge corpora**:  training complexity depends on the number of nonzero entries in $X$, not directly on corpus size. GloVe was trained on a 42-billion-token Common Crawl corpus in the original paper, achieving 75% accuracy on the word analogy task.

**Good performance even on small corpora**: unlike word2vec, which needs billions of tokens to learn robust vectors, GloVe captures global co-occurrence statistics efficiently and produces strong embeddings even from smaller datasets. This is because it processes every co-occurrence pair as an aggregate count, not as individual training instances.

###### Word Analogy Tests
After training, GloVe vectors support linear arithmetic that captures relational structure. The analogy "Beijing is to China as Delhi is to ?" is solved by:
$$
\vec{v}_{\text{Delhi}} - \vec{v}_{\text{Beijing}} + \vec{v}_{\text{China}} \approx \vec{v}_{\text{India}}
$$

The direction $\vec{v}_{\text{Beijing}} \to \vec{v}_{\text{China}}$ encodes the "capital of" relationship. Translating Delhi by the same vector lands near India. The original GloVe paper achieved 75% accuracy on a 19,544-question analogy test.

###### Bias in Embeddings

Word embeddings absorb and replicate biases present in the training text. Because embeddings are trained on human-generated text, they reflect the associations people actually write, including social stereotypes:
- father : computer programmer :: mother : **homemaker**
- man : doctor :: woman : **nurse**
- man : financial advisor :: woman : **homemaker**

These are not bugs inserted by the model, they emerge automatically from the statistics of the corpus. The model has no concept of fairness; it just faithfully encodes what co-occurs with what. Words like "doctor" appear more frequently near "man" in historical text, and "nurse" more frequently near "woman", so the embedding space reflects that gap. This is now an active research area: how to de-bias embeddings while preserving their semantic utility.
### Diachronic (Temporal) Word Embeddings

Train separate GloVe models on text from different decades, for example, the New York Times corpus split by decade from 1950 to 2010. Track how the embedding of a word moves through the vector space over time.

Classic example: **"broadcast"**
- In the 1850s, "broadcast" was an agricultural term — "to broadcast seeds" meant scattering them widely across a field. Its nearest neighbors were "sow", "scatter", "field".
- By the 1920s, with the rise of radio, "broadcast" migrated — its nearest neighbors became "radio", "transmit", "station".
- By the 1960s–70s, it fully shifted to television — nearest neighbors became "television", "network", "program".

Another example: **"gay"** — which shifted from primarily meaning "joyful" in early 20th-century text to its modern meaning by the late 20th century. The vector for "gay" literally moves in embedding space across decades, and its nearest neighbors change accordingly.

This technique turns word embeddings into a **lens for studying cultural and linguistic change**,  a field now called computational historical linguistics or diachronic NLP.