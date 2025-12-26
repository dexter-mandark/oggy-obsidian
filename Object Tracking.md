---
cssclasses:
  - callouts-outlined
---

###### Tags:

---

## Color Histograms

Each pixel has three color values $RGB$, the colors are distributed across **three independent dimensions** - the Red, Green, and Blue color channels. For a range of $(0, 255)$ for each channel, I'm looking at a total $256^3$ colors.

Now, if I decide to track an object by tracking the color of all the pixels in the object. I'll have too issues --
- I cant track these many colors, for each pixel, across multiple frames. *Too computationally heavy.*
- Exact pixel-level color matching is *too fragile.* When your target car moves between frames, lighting changes slightly

Solution ? **Quantization -- Color Histograms**
I group a range of similar colors into a single group (bin) and track that. I don't care if a pixel is exactly `RGB(200, 50, 30)` versus `RGB(202, 48, 32` -- both are "reddish" and would belong to the same bin. Having a bin of colors --
-  create a representation that's *tolerant to small variations* in illumination and sensor noise.
- transforms raw pixel data into a *compact statistical signature* of the object.

​
A **color histogram** is simply a count of how many pixels in your image have each color. It's a statistical summary that tells you the color distribution of your image.​

**Color histograms** are **relatively invariant** to translation, rotation, and small scale changes. When an object moves in the frame, its color distribution remains roughly constant, allowing me to track it by finding regions with matching histograms. The histogram acts as a feature vector that characterizes the object independent of its exact spatial configuration.

>[!tip]
>If I consider the color space as a cube, then every possible color is a point somewhere in this cube. When I quantize the space with $b_{pc}$ bins per channel, I'm dividing this cube into smaller sub-cubes.
>
>If $b_{pc} = 4$, then a space of $256 \times 256 \times 256$ is divided into $64$ small cubes, where each cube is $4 \times 4 \times 4$ in size. Each of these $64$ sub-cubes is a *histogram bin* that represents a range of similar colors.
>
>The histogram then counts how many pixels in my object (region of interest) have colors that fall into each bin.

###### Define Region of Interest (ROI)

I select the pixels I want to analyze. For tracking, this would be the object bounding box. Let there be $n$ pixels in this region: $\{x_{1},x_{2},…,x_{n}\}$​

###### Choose Bin Structure

Now I have to decide how many bins per channel ($b_{pc}$).
- $b_{pc} = 256$: No quantization, one bin per intensity value (very detailed but large)
- $b_{pc} = 16$: $16^3 = 4096$ total bins (moderate)
- $b_{pc} = 4$: $4^3 = 64$ total bins (compact)

 And the bin size $b_s$, that is the number of color values (for each channel) that can be in each bin, is given by $$b_s = \frac{256}{b_{pc}}$$

###### Build the histogram

I'll have a total of $b_{pc}^3$ bins in total. I need a bin-index which would decide what bin a pixel goes into. For a 1-D histogram, the bin index $b(x_i)$ is given as: $$b(x_i) = \lfloor \frac{x_i - x_{min}}{b_s}\rfloor +1$$
But here the bin is 3-D, takes three values. What I want is a 1-D bin-index, which is given as -
$$b(x_i) = \lfloor \frac{R}{b_s} \rfloor \times b_{pc}^2 + \lfloor \frac{G}{b_s} \rfloor \times b_{pc}^1 + \lfloor \frac{B}{b_s} \rfloor \times b_{pc}^0$$
Where $I(x_i) = (R, G, B)$ are the color values for each channel of the pixel.

>[!tip]
>What I'm basically doing is considering $I(x_i)$ as 3 digit number in base-$256$ and I'm converting that to a 3 digit number in base-$b_{pc}$


I then create an array $H[j]$ where $j = 0, ..., (b_{pc}^3 - 1)$, initialized to zeros. This will store the counts of how many pixels fall into the bin $j$.

$$H[j] = \frac{1}{n}\sum_{i=1}^{n}\delta\Big[b(x_i) - j\Big]$$

###### Weighted Epanechnikov Kernel Histogram

With a simple histogram, I treat all pixels equally - *a pixel at the center of the object window gets the same weight as a pixel at the edge*. This creates problems for tracking:​
- **Edge pixels are unreliable**: Pixels near the boundary of the tracking window might be partly background, especially if the bounding box isn't perfectly tight around the object.
- **Occlusion sensitivity**: If part of the object gets occluded at the edges, those pixels drastically change the histogram even though the core object hasn't changed.

Instead of counting each pixel equally, I assign **weights based on spatial location**. Pixels near the center of the tracking window get higher weights, pixels near the edge get lower weights.

The **Epanechnikov kernel** works best here:
- It minimizes mean squared error in density estimation - it's provably the best kernel for this purpose.
- It equals zero outside a certain radius, meaning I only process pixels within the tracking window.
- It decreases smoothly from center to edge, creating a "soft" weighting that's robust to small position errors.

$$\boxed{\omega(x_i) = c_E \left(1 - \frac{(x_i-x_c)^2}{s_x^2} - \frac{(y_i-y_c)^2}{s_y^2}\right)}$$
Where $(x_c, y_c)$ is the center of the tracking window, and $s_x, s_y$ define the window size.

And instead of the simple histogram, I use this new weighted histogram.
$$\boxed{h[j] = \frac{1}{C_h}\sum_{i=1}^{n} \omega(x_i).\delta\Big[b(x_i) - j\Big]}$$
Where, $C_h$ is simply the sum of weights.
$$\boxed{C_h = \sum_{i=1}^{n} \omega(x_i)}$$
When I encounter a pixel with color in bin $j$, instead of adding $1$ to $H[j]$, I add $\omega(x_i)$ to $h[j]$. A center pixel might contribute $0.9$, while an edge pixel might contribute only $0.1$.

## Target and Candidate Models

#### Target Model

The **target model** is the reference histogram built from the initial frame where I first locate the object, its the weighted color histogram of my ROI.
For the pixels in my ROI $\{x_{1},x_{2},…,x_{n}\}$, the target model is:

$$\large q[j] = \frac{1}{C_q}\sum_{i=1}^{n} k_E(\|x_i\|^2) .\delta\Big[b(x_i) - j\Big]$$

- **$k_E(\|x_i\|^2)$**: The Epanechnikov kernel weight for pixel $x_i$, where $\|x_i\|^2$ is the squared distance of pixel $x_i$ from the target center.​
- $C_q$: Normalization constant $C_q = \sum_{i=1}^{n} k_E(\|x_i\|^2)$, ensuring the histogram sums to 1.

#### Candidate Model

The **candidate model** is built at a hypothesized location $y$ in the current frame to see if the object is there.​

I start with a candidate center position $y=(y_x,y_y)$ Initially, this could be:
- The target location from the previous frame.
- A predicted location (if using motion models like Kalman filter).

Then, I collect *all the pixels within the same size window* (as the target region) centered at $y$.

$$\large p_y[j] = \frac{1}{C_p}\sum_{i=1}^{n} k_E\left(\left\|\frac{x_i - y}{h}\right\|^2\right)\delta\Big[b(x_i) - j\Big]$$

- $k_E\left(\left\|\frac{x_i - y}{h}\right\|^2\right)$: The Epanechnikov kernel weight, but now measuring distance from the **candidate center location $y$.**
- $h$: The **bandwidth** parameter (kernel size/window half-width) that normalizes the distance. This controls how quickly the weights decay with distance.

*For each bin $j$, look at all pixels around the candidate location $y$, weight each pixel by its distance from $y$ (scaled by bandwidth $h$), and count how many pixels have colors in bin $j$*

>[!danger] No y is target
>The target is in its *own coordinate system* where the center is at the origin, so distances are just $\|x_i\|$. The candidate is located at some position $y$ in the *image coordinate system*, so distances are $\|x_i - y \|$. The bandwidth $h$ makes the candidate formula more general and allows me to search at different scales.

## Comparing target and candidate models

#### Bhattacharyya coefficient

> [!cite] Bhattacharyya coefficient
> The Bhattacharyya coefficient (BC) is a **similarity measure** that quantifies how **much two probability distributions overlap.**
>
> For two discrete probability distributions $(p(x), q(x))$, the Bhattacharyya coefficient $\rho(p, q)$, based on whether p, q are continuous or discrete probability distributions, is defined as:
> $$\begin{align} \rho(q, p) &=  \sum_{i=1}^{n} \sqrt{p_i\cdot q_i} \\ \rho(p, q)&=\int \sqrt{p(x)q(x)}​dx \end{align}$$
>
> Where $0 \leq \rho(p, q) \leq 1$, the closer it is to $1$, the more overlap (more similar) between the two probability distributions.
>
> BC creates a **smooth, differentiable similarity function** and is **symmetric**, $\rho(p, q) = \rho(q, p)$

The BC computes the **geometric mean** of corresponding bin pairs, then sums them up. It essentially measures how much "probability mass" the two distributions share.
It serves as a good measure for comparing the target and the candidate model.
- Unlike Euclidean distance or other metrics, the Bhattacharyya coefficient is specifically tailored for comparing **normalized frequency distributions**.
- Many histogram bins may be zero (especially with fine quantization). The square root operation $\sqrt{q[j] \cdot p_y[j]}$ handles zeros naturally without causing division-by-zero issues that plague other metrics like chi-squared.​
- BC creates a **smooth, differentiable similarity function** as I move the candidate window across the image. This smooth surface is essential for mean shift to work - the algorithm can compute gradients and follow them to find the maximum similarity location.​
- The square root operation makes BC *less sensitive to absolute histogram values* and more focused on the **shape of the distribution**. Small lighting changes that slightly shift bin counts don't dramatically affect the coefficient.​

#### What to do ?

I want to find the location $y$ where the candidate histogram $p_y$ best matches the target histogram $q$. This means **maximizing** the Bhattacharyya coefficient, given as:
$$\large \rho(y) = \sum_{j=0}^{m-1} \sqrt{q[j]. p_y[j]}$$
Where $m=b_{pc}^3$

*The challenge: I can't just compute this at every possible location $y$ - that would be too slow.* Instead, I use **gradient ascent** to efficiently climb toward the maximum.​
$$\large y_{k+1} = y_k + \eta_k \nabla_y\rho(y)$$

#### Computing the Gradient $\nabla_y \rho(y)$

The gradient of BC is given by:
$$\begin{align} \nabla_y \rho(y) &= \nabla_y \left\{ \sum_{j=0}^{m-1} \sqrt{ q[j] \, p_y[j] } \right\} \\ \\  &= \nabla_y \left\{ \sum_{j=0}^{m-1} \sqrt{ \frac{q[j]}{p_y[j]} } \, p_y[j] \right\} \\ \\ &= \sum_{j=0}^{m-1} \sqrt{ \frac{q[j]}{p_y[j]} } \, \nabla_y p_y[j] \end{align}
$$

>[!tip] Why is the $p_y[j]$ in the denominator not subject to the gradient ?
>Approximation. **First-order Taylor approximation**.
>
>The $p_y[j]$ in the denominator is considered to be the same as $p_{y_0}[j]$. The approximation assumes I'm moving $y$ only slightly from $y_0$ in each iteration. Essentially making the term $\sqrt{ \frac{q[j]}{p_y[j]} }$ a constant value.

For the candidate model $p_j[y]$, the gradient is:
$$\begin{align} \nabla_y p_y[j] &= \nabla_y \left\{ \sum_{i=1}^{n} k\!\left( \left\| \frac{x_i - y}{h} \right\|^2 \right) \delta[b(x_i)-j] \right\} \\ \\ \nabla_y p_y[j] &= \frac{2c}{h^2} \sum_{i=1}^{n} (x_i - y)\,g\!\left( \left\| \frac{x_i - y}{h} \right\|^2 \right) \delta[b(x_i)-j]\end{align}
$$
Where, $g_E$ is the shadow kernel (the negative derivative of the kernel profile $k_E$)
​
Now, I can substitute the value of $\nabla_y p_y[j]$ in $\nabla_y \rho(y)$, which is:

$$\begin{align} \nabla_y \rho(y) &= \sum_{j=0}^{m-1} \sqrt{ \frac{q[j]}{p_y[j]} } \, \nabla_y p_y[j]  \\ \\ &= \frac{2c}{h^2}\sum_{i=1}^{n}(x_i - y)g\left(\left\|\frac{x_i - y}{h}\right\|^2\right)\sum_{j=0}^{m-1}\sqrt{\frac{q[j]}{p_y[j]}}\delta[b(x_i) - j] \end{align}$$

This looks too complex, so I break it down.
$$\large \boxed{w_i = \sum_{j=0}^{m-1}\sqrt{\frac{q[j]}{p_y[j]}} \,\delta[b(x_i) - j]}$$

This $w_i$ is essentially the weight: *How much does pixel $i$ help match the target?*
$\sqrt{\frac{q[j]}{p_y[j]}}$: This ratio tells me how "important" each pixel is. If the target has a lot of a particular color (high $q[j]$) but the candidate doesn't (low $p_y[j]$), this ratio is large, making pixels with that color highly weighted.

I can represent the shadow kernel as:
$$g_i(y) = g\left(\left\|\frac{x_i - y}{h}\right\|^2\right)$$
And finally,

$$\large \boxed{\nabla_y \rho(y)= \frac{2c}{h^2} \sum_{i=1}^{n} (x_i - y)\, g_i(y)\,w_i}$$

#### Ascend the hill

In the gradient ascend equation, simply substitute the value of $\nabla_y \rho(y)$ as:
$$\begin{align} \large y_{k+1} &= y_k + \eta_k \nabla_y\rho(y_k) \\ \\ &= y_k + \eta_k \left ( \frac{2c}{h^2} \sum_{i=1}^{n} (x_i - y_k)\, g_i(y_k)\, w_i \right) \\ \\ y_{k+1} - y_k &= \eta_k \left ( \frac{2c}{h^2} \sum_{i=1}^{n} (x_i - y_k)\, g_i(y_k)\, w_i \right)\end{align}$$