Here is the continuation in your exact notation, picking up right where you left off.

***

And as $X_{ik} = X_{ki}$ (co-occurrence is symmetric), I can add both equations to get:

$$
w_i^\top \tilde{w}_k + w_k^\top \tilde{w}_i = 2\log X_{ik} - \log X_i - \log X_k
$$

The left side is two dot products — both equal $\log X_{ik}$ in the target, just from opposite directions. Divide both sides by 2:

$$
\frac{w_i^\top \tilde{w}_k + w_k^\top \tilde{w}_i}{2} = \log X_{ik} - \frac{1}{2}\log X_i - \frac{1}{2}\log X_k
$$

Now look at the right side. The terms $\frac{1}{2}\log X_i$ and $\frac{1}{2}\log X_k$ are **word-specific constants** — $\frac{1}{2}\log X_i$ depends only on word $i$ (its total co-occurrence count with everything), and $\frac{1}{2}\log X_k$ depends only on word $k$. They tell us nothing about the specific relationship between $i$ and $k$. Rather than computing them from raw counts at every step, GloVe absorbs them into **learnable scalar biases** $b_i$ and $b_k$:

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

This is the **GloVe prediction equation**. The dot product of word $i$'s target vector $w_i$ and word $k$'s context vector $\tilde{w}_k$, plus one scalar bias per word, should equal the log of how many times words $i$ and $k$ co-occurred in the corpus.

***

### The Loss Function

Cast this as a least-squares problem — for every pair $(i, k)$ in the vocabulary, minimize the squared difference between what the model predicts and what the data says:

$$
\mathcal{L} = \sum_{i,k=1}^{|V|} \left(w_i^\top \tilde{w}_k + b_i + b_k - \log X_{ik}\right)^2
$$

But this treats all pairs equally — a pair that co-occurred once gets the same training signal as a pair that co-occurred a million times. That is wrong. So we introduce a weighting function $f(X_{ik})$:

$$
\boxed{\mathcal{L} = \sum_{i,k=1}^{|V|} f(X_{ik})\left(w_i^\top \tilde{w}_k + b_i + b_k - \log X_{ik}\right)^2}
$$

The learnable parameters are: $w_i$ (rows of $W$), $\tilde{w}_k$ (rows of $W'$), $b_i$ (scalar bias for target word $i$), and $b_k$ (scalar bias for context word $k$). The co-occurrence matrix $X$ is computed once before training and held completely fixed. Minimize $\mathcal{L}$ using gradient descent.