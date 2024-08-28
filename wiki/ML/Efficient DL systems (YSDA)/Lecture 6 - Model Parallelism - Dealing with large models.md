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


## What if a model is larger than GPU?
### Easy mode**: cannot fit the right batch size
> ![[Pasted image 20240827195442.png]]

 Loss is divided by global steps for them to be global avg grads (optional)
  For some networks this is not equal to big batches (e.g. contrastive learning, batchnorm) 
### **Hard mode**: cannot fit a single sample
> Gradient checkpointing:
> ![[Pasted image 20240827200136.png]]


 - By default - we remember all inputs for all layers
 - Grad checkpointing selectively deletes activations (e.g. every 5th, 10th, ...)
 - As activations are deleted -> recompute them from the last remembered layer
 !!!! For some layers (e.g. Dropout) with the same inputs here can be different outputs => set seed/fix random state (done by torch by default)
### **Expert mode**: not even parameters (model is larger than GPU)
Store some unnecessary numbers during some periods of computation (e.g. Adam weights during forward pass)
#### memory offloading
##### L2L-offloading
> ![[Pasted image 20240827205930.png]]
> 

in `torch`: threads (write to different streams) or `torch.cuda.Stream`

less slowdown while working with larger batch size - time for loading params doesn't depend on batch size
##### ZeRO-offload
> ![[Pasted image 20240827210746.png]]

only weights are on gpu or only some parameters, etc.

> ![[Pasted image 20240827210952.png]]
> Can introduce staleness (like in parameter server)


%% ##### Framework DeepSpeed %%


## Model Parallelism
### Pipeline Parallelism
##### Sequential
> ![[Pasted image 20240827211638.png]]

During a single time event only a single GPU is working, others are waiting ==> Shard batch on microbatches


##### Bubble - Pipeline Parallelism
> ![[Pasted image 20240827211956.png]]
> First and last pieces will always be unparallelled
> Bubble - underutilization


##### Pipeline-Parallel training
> ![[Pasted image 20240827212329.png]]
> e.g. ViT on ImageNet decrease performance


### Tensor Parallelism
Some operations are element-wise. For matrix multiplications - e.g. chunk weight matrices or chunk by the inputs
> ![[Pasted image 20240828000320.png]]
> Needs more communications and synchronizations

* Split by output neurons -> concatenation as communication 
* Split by input neurons -> summation as communication

For instance Consider torch implementation of Linear layer: $y = xA^{T} + b$. We can implement tensor parallel for 2 gpus as follows:
> ![[Pasted image 20240827235712.png]]
> Description:

Inputs are stored completely on all GPUS. First linear layer - GPU1 computes upper half of $y_{1}$, GPU2 - lower half of $y_{1}$ (split $A_{1}$ by rows). ReLU - can be done in parallel. Second Linear layer - GPU1 computes first partial $y_{2,1}\in \mathbf{R}^{d_{\text{out}}}$, GPU2 computes second partial $y_{2,2}\in \mathbf{R}^{d_{\text{out}}}$ (split $A_{2}$ by columns). Only **one communication** at the end - allreduce: $\hat{y} = y_{2, 1} + y_{2, 2}$


### Model parallelism overall
> ![[Pasted image 20240828000450.png]]
- [ ] Watch these tutorials



* Pipeline parallelism doesn't offer benefit  for a single given input - it's still processed by all GPUs => tensor parallelism has a potential to speed up (e.g. in the example above tensor parallelism offers half less FLOPS per GPU). 
* **Good idea:** Tensor parallelism inside one server, remaining layers - on the other server. Between the servers - pipeline parallelism.
* **Good idea:** Can use Data parallelism if you have remaining compute power. It's also preferable for the cases where a model fits on a single GPU

**Q: What if you have 1024 GPUs, but the model fits on 8?**
- Large-scale training: combine model- and data-parallel

Can be done like that: Tensor parallel $\implies$ Pipeline parallel $\implies$ Data Parallel (as DDP scales better than pipelines)

### ZeRO
> ![[Pasted image 20240828004911.png]]

FSDP - fully sharded data parallel - no need to rewrite code

#### Sharded Data Parallelism
- model is sharded
- data is parallelied

See ZeRO movie:
> ![[Pasted image 20240828003721.png]]


### Thoughts
- small batch - tensor parallelism & ZeRO are more efficient
- FSDP




**Data parallelism - *model* moves, model parallelism -  *data* moves**

## The square-cube law of deep learning
> ![[Pasted image 20240828005723.png]]
Hidden size grows - communication is `batch * hidden`. Communication


## Example configuration

> ![[Pasted image 20240828142541.png]]

If a model doesn't fit, one can quantize it too :)