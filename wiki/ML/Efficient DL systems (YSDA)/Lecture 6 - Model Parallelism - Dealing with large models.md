---
created:
  - " 08-03-2024 16:38"
aliases: 
tags:
  - article/
---

# Lecture 6 - Dealing with large models

> ![[Pasted image 20240827195014.png]]
> ![[Pasted image 20240827195131.png]]


### What if a model is larger than GPU?
- **Easy mode**: cannot fit the right batch size
> ![[Pasted image 20240827195442.png]]
> Loss is divided by global steps for them to be global avg grads (optional)
> For some networks this is not equal to big batches (e.g. contrastive learning, batchnorm)
- **Hard mode**: cannot fit a single sample
> Gradient checkpointing:
> ![[Pasted image 20240827200136.png]]
> - By default - we remember all inputs for all layers
> - Grad checkpointing selectively deletes activations (e.g. every 5th, 10th, ...)
> - As activations are deleted -> recompute them from the last remembered layer
- **Expert mode**: not even parameters

## Chapter 1

