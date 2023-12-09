---
created:
  - " 09-12-2023 16:57"
aliases: 
tags:
  - article/
---

# QUIK

## What is that?

> [QUIK](https://arxiv.org/abs/2310.09259) is an extension of [[GPT-Q]].  It's main feature is that, in addition to model quantization, it also quantizes activations. That way, multiplication can be performed in `int`, leading to 2x-3x improvement in inference speed over GPTQ, which dequantizes the weights and performs multiplication in `float`.


### Different Range, Different Scales and Zeros

At basic quantization level, QUIK already has a number of differences compared to GPTQ:

1. QUIK quantizes the weights to **signed** integer values for better stability of matrix multiplication.

2. QUIK enforces `zero` to be integer, to be able to perform `int` multiplication with it as well.

3. QUIK changes the sign of `zero` (compare `quik_dequantize` with `dequantize`) for simplicity.