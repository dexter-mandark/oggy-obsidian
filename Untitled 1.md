Great call — let's slow down and build this properly from the ground up.

***

## What Is a Language Model?

A language model is a system that assigns a **probability to any sequence of words (or tokens)**. That's it — at its core, it answers the question: *"How likely is this sequence of words to appear in natural language?"*

For example, given these two sentences:
- "The cat sat on the mat" → high probability
- "Mat the on sat cat the" → near-zero probability

A language model should assign the first sentence a much higher probability than the second, because the first matches the statistical patterns of real English. This probability assignment is what makes language models useful for autocomplete, translation, speech recognition, and generation.

***

## Tokens: The Building Blocks

Before talking about probabilities, you need to define what you're assigning probabilities *to*. A **token** is the basic unit the model operates on. Tokens can be:

- **Characters**: `h`, `e`, `l`, `l`, `o`
- **Words**: `hello`, `world`
- **Subwords**: `hel`, `lo` — used by modern systems like GPT and BERT
- **Numbers or special symbols**: `<s>` (sentence start), `</s>` (sentence end), `<UNK>` (unknown)

The choice of tokenization deeply affects the model. Character-level models have tiny vocabularies (~27–100 symbols) but very long sequences. Word-level models have large vocabularies (~50,000–100,000 words) but shorter sequences. Most modern LLMs use subword tokenization as a middle ground.

***

## Formalizing the Problem

Let's say you have a sentence of $n$ words: $w_1, w_2, \dots, w_n$. The language model needs to compute:

$$
P(w_1, w_2, \dots, w_n)
$$

This is a **joint probability** — the probability that *all* these words appear together in this order. Now, how do you actually compute this? You can't just look up "the probability of this exact sentence" in a table — there are infinitely many possible sentences.

The mathematical tool that solves this is the **chain rule of probability**.

***

## The Chain Rule of Probability

The chain rule is a fundamental identity from probability theory. For any sequence of events:

$$
P(A, B) = P(A) \cdot P(B \mid A)
$$

Extend this to three events:

$$
P(A, B, C) = P(A) \cdot P(B \mid A) \cdot P(C \mid A, B)
$$

Applied to a word sequence, this gives:

$$
P(w_1, w_2, \dots, w_n) = P(w_1) \cdot P(w_2 \mid w_1) \cdot P(w_3 \mid w_1, w_2) \cdots P(w_n \mid w_1, \dots, w_{n-1})
$$

Or more compactly:

$$
P(w_1, w_2, \dots, w_n) = \prod_{i=1}^{n} P(w_i \mid w_1, w_2, \dots, w_{i-1})
$$

This decomposition is **exact** — no approximation yet. It just says: the probability of a whole sentence equals the probability of the first word, times the probability of the second word *given* the first, times the probability of the third word *given* the first two, and so on.

Each term $P(w_i \mid w_1, \dots, w_{i-1})$ is a **conditional probability**, and the sequence $w_1, \dots, w_{i-1}$ is called the **context** or **history**.

***

## Worked Example

***

## Estimating Probabilities From a Corpus

The natural approach is **Maximum Likelihood Estimation (MLE)** — you count how often things appear in a large training corpus and turn counts into probabilities. For example:

$$
P(\text{monsoon} \mid \text{the}) = \frac{\text{Count("the monsoon")}}{\text{Count("the")}}
$$

If "the" appears 1,000,000 times in your corpus and "the monsoon" appears 3,200 times, then:

$$
P(\text{monsoon} \mid \text{the}) = \frac{3200}{1000000} = 0.0032
$$

This is perfectly clean and intuitive. The problem arises as the **context grows longer**.

***

## The Exponential Blow-Up Problem

For the last term in our example, you need:

$$
P(\text{begun} \mid \text{the, monsoon, rain, has})
$$

You need to count how many times the exact 4-word phrase *"the monsoon rain has"* appears in the corpus, and among those occurrences, how many are followed by "begun". In any realistic corpus — even one with billions of words — this exact 4-word phrase has likely appeared **zero or one times**.

This is not a dataset size problem; it's a **combinatorial explosion** problem. If your vocabulary has $V = 50,000$ words, then:

- Possible bigrams (2-word contexts): $50,000^2 = 2.5 \text{ billion}$
- Possible trigrams: $50,000^3 = 125 \text{ trillion}$
- Possible 5-grams: $50,000^5$ — a number so large no corpus could ever cover it

As the context length grows by 1, the number of possible context strings multiplies by 50,000. No corpus in existence covers more than a vanishingly small fraction of all possible long contexts.

***

## Why Zero Counts Are Catastrophic

Suppose just one of the conditional probability terms in the chain rule product comes out to zero — perhaps because "the monsoon rain has" was never seen in training. Then:

$$
P(\text{the, monsoon, rain, has, begun}) = P(\text{the}) \cdot P(\text{monsoon} \mid \text{the}) \cdot \underbrace{P(\ldots) \cdots P(\text{begun} \mid \ldots)}_{\text{one term} = 0} = 0
$$

The **entire sentence probability collapses to zero**, even if the sentence is perfectly grammatical and natural. This is a fatal failure for any application: a spell-checker would flag the sentence, a speech recognizer would reject it, a language generator could never produce it.

***

## The Two Types of Zero

It's important to distinguish *why* a count is zero, because not all zeros are the same problem:

- **Structural zeros**: Word combinations that are genuinely impossible, like two consecutive transitive verbs ("eat want") or grammatically illegal sequences. These *should* have zero probability — the model is correct to assign zero here.
- **Contingent zeros (false zeros)**: Valid, natural word combinations that simply never appeared in the training data due to limited corpus size — like "enjoyed the monsoon" if your corpus only has "enjoyed the film" and "enjoyed the festival." These are the dangerous zeros: the real world probability is nonzero, but the model is assigning zero because of data sparsity.

The contingent zeros are what make MLE with full context unusable in practice. This is the fundamental **data sparsity problem**, and it is the reason the Markov assumption and n-gram models were invented — which is what comes next.

***

The entire machinery of n-gram models, smoothing, back-off, and interpolation that the lecture covers is essentially a series of increasingly clever answers to this one core problem: **how do you estimate conditional probabilities reliably when most long-context combinations are never seen in training?**

