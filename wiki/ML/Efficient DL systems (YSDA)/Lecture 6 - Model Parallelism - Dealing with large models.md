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
- **Expert mode**: cannot fir a single sample

- **Expert mode**: not even parameters

## Chapter 1

