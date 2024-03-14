---
created:
  - " 14-03-2024 18:06"
aliases: 
tags:
  - article/
---

# Lecture 9 - Efficient model inference

## 

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

## Knowledge distillation
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








