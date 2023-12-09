---
created:
  - " 09-12-2023 16:57"
aliases: 
tags:
  - article/
---

# QUIK

## What is that?

> [QUIK](https://arxiv.org/abs/2310.09259) is an extension of [[GPT-Q]]. 

It's main feature is that, in addition to model quantization, it also quantizes activations. That way, multiplication can be performed in `int`, leading to 2x-3x improvement in inference speed over GPTQ, which dequantizes the weights and performs multiplication in `float`.