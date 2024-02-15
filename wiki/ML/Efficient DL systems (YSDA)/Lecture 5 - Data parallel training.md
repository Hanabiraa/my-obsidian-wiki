---
created:
  - " 15-02-2024 18:08"
aliases: 
tags:
  - article/
---

# Lecture 4 - Data parallel training

### MR Rules


![[Pasted image 20240215181150.png]]

### Summary: operation parallelism
![[Pasted image 20240215181420.png]]


### Data-Parallel training (naive)
- moving batch to the GPU and model every step - can be slow
![[Pasted image 20240215182228.png]]


If more GPUs - let's replicate for every GPU - can be slow for everyone at the same time


Replicate all gradients on CPU - comparable with model weights transfer
![[Pasted image 20240215182628.png]]


### All-Reduce data parallel
![[Pasted image 20240215183036.png]]

- **ALLREDUCE** operation \[sum\]- every GPU has full gradients for each every parameter
- some redundant computation (4 steps instead of 1)

#### Faster All-Reduce


![[Pasted image 20240215183426.png]]
- linear time of the allreduce

##### **Tree-allreduce**
![[Pasted image 20240215183449.png]]
- sum is parallel
- **logarithmic time** of the workload of allreduce

##### **Butterfly-allreduce**
![[Pasted image 20240215184157.png]]
- split into chunks and send them
- GPU-1 gets $\frac{n-1}{n}$ of gradients
- parallely average on every GPU
- get and send $\frac{n-1}{n}\to{1}$ of gradients
- all should have overall sum - every GPU gets part of gradients from other GPUs
- **constant time** (but costly)

Effective all-reduce is more affective with more GPUs

***Problems with lots of GPUs*** - lots of connections between servers



#### Ring Data parallel all-reduce by YSDA:
- at first half of rounds - communicate each part 
![[Pasted image 20240215191449.png]]

also horovod, nccl - frameworks

#### Torus all-reduce
Allreduce row-wise and column-wise $2\sqrt{rounds}$


## All-reduce data parallel VS reality

![[Pasted image 20240215192817.png]]

What to do? - Async, decentralized (for SGD is very well)


### Stochastic Gradient Push
![[Pasted image 20240215193050.png]]
![[Pasted image 20240215193058.png]]
![[Pasted image 20240215193137.png]]


## Data-parallel recap
![[Pasted image 20240215193629.png]]

LAMB - optimizer for VERY large batches

## What if sending tensors during AllReduce takes too long?

### Quantized communication
#### Error Feedback
![[Pasted image 20240215195541.png]]

`
















`

![[Pasted image 20240215195556.png]]


Feed grads with error at the next time:
![[Pasted image 20240215195700.png]]




![[Pasted image 20240215195923.png]]


### Zero-redundancy optimizer

