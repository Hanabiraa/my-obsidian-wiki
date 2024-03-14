---
created:
  - " 14-03-2024 18:06"
aliases: 
tags:
  - article/
---

# Lecture 9 - Efficient model inference

## Distillation

Pareto curve Quality vs Compute

> ![[Pasted image 20240314180950.png]]

#### Compute bound vs Memory bound
> ![[Pasted image 20240314181140.png]]

> ![[Pasted image 20240314181205.png]]

Bandwidth - how many weights we carry to GPU and preprocess

### Flops vs Bandwidth utilization
TPOT - Time per output token
MPU - memory bandwidth utilization
MFU - memory FLOPs utilization
> ![[Pasted image 20240314181318.png]]

### Knowledge distillation
**General framework:**
> ![[Pasted image 20240314181906.png]]

Generate these labels and pairs - " a.k.a. generate training set" 
> ![[Pasted image 20240314182017.png]]


Try to reproduce logits:
> ![[Pasted image 20240314182032.png]]
> generate from teacher and learn its logits


> ![[Pasted image 20240314182150.png]]


**TinyBert** - effective BERT distillation:
> ![[Pasted image 20240314182436.png]]
- losses on hiddens, attention maps, embeddings


**KL-Divergence can be bad for distillation of LLM:**
> ![[Pasted image 20240314182919.png]]
> - Intiutively, small model lacks expressivity of compute to distinguish teacher's distribution
> - Make reverse-KL-divergence - and student will learn at least one mode:
> ![[Pasted image 20240314183057.png]]
**Problem** - now expected value is across student distribution -> unable to differentiate by y
> ![[Pasted image 20240314183221.png]]
> Solution - get RL, but neeeds thorough hyperparams optimization


#### Better solution
> ![[Pasted image 20240314183823.png]]


#### CE RL
> ![[Pasted image 20240314184141.png]]
- RL instead of distillation
- Reward model learns from users feedback and then scores quality of generation

**YA experience:**
> ![[Pasted image 20240314184421.png]]
- can spend more time for distill - win in the long run (less compute afterwards on distilled small model)


## Quantization

### Formalization and benefits
> ![[Pasted image 20240314184803.png]]
- Convert FP16 to INT
- Less GPU RAM for compute (at least 2 times)
- increase latency and RPS
- WxAy - weights to $x$ bits, activations to $y$ bits

### Quantization types
**PTQ - post training quantization**
**QAT - quantization-aware traiing** - train and get passes in INTS
> ![[Pasted image 20240314185101.png]]

Quantization scheme types
> ![[Pasted image 20240314195013.png]]
*Data-free - without calibrational data*

### Problems
#### Quality drops (*old paper*)
> ![[Pasted image 20240314185333.png]]
- easier to quantize weights, harder - activations

#### Outliers
> ![[Pasted image 20240314185618.png]]
- huge outliers - very large magnitude of column/row in weights matrix
**Solution method:**
> ![[Pasted image 20240314190137.png]]
> migrate outliers to weights:
> ![[Pasted image 20240314190157.png]]
> ![[Pasted image 20240314190417.png]]
> ![[Pasted image 20240314190740.png]]

quantization to INT8 basic structure:


[[GPT-Q]]:
> ![[Pasted image 20240314190941.png]]




## ??? Encoder-decoder vs decoder
**Who is faster with the same amount of parameters on inference?**
> ![[Pasted image 20240314191601.png]]
- on the classification - both $\pm$ identical
- on the generation - forward for each token each decoder layer - more optimal to use encoder-decoder (more memory-bandwidth), as there is less decoder layers, and in distillation, there are less decoder layers in encoder-decoder architecture and the quality $\pm$ similar

BUT encoder-decoder is more costly to learn

## Conclusion
![[Pasted image 20240314193611.png]]