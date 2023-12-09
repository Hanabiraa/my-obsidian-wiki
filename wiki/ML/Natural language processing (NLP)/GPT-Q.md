---
created:
  - " 09-12-2023 12:45"
aliases: 
tags:
  - article/
---

# GPT-Q

## What about its structure?

> GPTQ is the State Of The Art quantization algorithm (*see [[wiki/ML/Natural language processing (NLP)/Untitled|Untitled]] for quantization*) for post-training DL model quantization. It works by sequentially quantizing the model's linear layer weights.

  

Although in outputs results similar to what one would get with Round To Nearest quantization, it makes a key observations to boost it's end performance:

* It is layer input aware (also referred to as "1-Shot" method), meaning int optimizes the quantized matrix to show best performance on inputs typically encountered in that layer.

### More formal problem formulation
More formally, the problem can be formulated as:

$$

W_q = argmin_{\widehat{W}}\|XW^T - X\widehat{W}^T\|_2^2,

$$ 
Where:

* $X$ is the input matrix of shape `(..., IN)`.

* $XW^T$ is the unquantized output of shape `(..., OUT)`. **We think of the norm above as taking a sum over those (...) dimensions.**

* $W$ is the unquantized weight of shape `(OUT, IN)`.

* $\widehat{W}$ is the quantized weight taken from some quantization grid.

  
#### How to solve it?
- One can notice that the expression above is independent with regard to the rows of $W$ and $\widehat{W}$, meaning we **can solve it for each row in parallel**. 
- This is the reason why we're working with row-wise quantization in the first place. Notice that the quantization grid only depends on min/max values withing the row and not the quantization process, so we can think of it as fixed.
- The dimension of the optimization problem is `IN`, which is too much to solve exactly. The algorithm proposes to solve it **iteratively**.

  

Less us consider a vector of full precision weights $F$ and corresponding set of inputs $X_F$. The corresponding objective is quadratic with Hessian

$$

H_F = 2X_F^TX_F^.

$$

#### The algorithm
The algorithm can be described like this:

* Do the following steps until $F$ is fully quantized:

1. Given the next index to quantize $i$, and corresponding unquantized element $F_i$.

2. Quantize the coordinate by prjecting it onto the quantization grid $Q_i = quant(F_i)$.

3. Update all of the remaining weights $F_: = F_: - \frac{F_i - quant(F_i)}{\left[H_F^{-1}\right]_{ii}}\cdot\left[H_F^{-1}\right]_{i,:}$.

4. Exclude $i$ from $F$.

  

It uses the inverse Hessian to slightly tune the remaining unquantized weights to mitigate the quantization error.

  

As for how $i$ is chosen, an observation was made that iterating over indices in order of **decreasing diagonal Hessian elements** provides the best performance.

  

There are a few more ideas that make this algorithm much faster:

1. We can represent the order of quantization (selection of $i$) by permuting the row in advance, and then iterating over the row element in order.

$$

F_{i:} = F_{i:} - \frac{F_{i} - quant(F_{i})}{\left[H_F^{-1}\right]_{ii}}\cdot\left[H_F^{-1}\right]_{i,i:}

$$

2. The problem is row-wise independent, meaning that we use the same permutation each row and perform those operations in a vector fashion for all the rows at the same time.

$$

F_{:,i:} = F_{:,i:} - \frac{F_{:,i} - quant(F_{:,i})}{\left[H_F^{-1}\right]_{ii}}\odot\left[H_F^{-1}\right]_{i,i:}

$$

3. We don't actually need to recompute the inverse Hessian. At $i$-th step we only need its $t$-th row, and we can use fancy math to precompute the matrix containing all of those rows in advance.

$$

H^{-1} = Cholesky(H^{-1})^T

$$

  

4. We don't need to tune all the remaining unquantized values right away. We can only apply the updates for the closest elements right away and accumulate all the other updates to apply them only once in a while.

  

We'll do this in block of fixed size, applying the updates inside of those blocks and updating the weights outside only when we're done with the block. To accumulate those updates, we'll collect the scaled quantization error

$$

Err_{:,i} =\frac{F_{:,i} - quant(F_{:,i})}{\left[H_F^{-1}\right]_{ii}}\text{ for all }i\text{ in block}.

$$


## See also:

[[QUIK]]