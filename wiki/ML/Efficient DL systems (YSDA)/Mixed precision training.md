---
created:
  - " 11-02-2024 11:46"
aliases: [FP16, FP32, BF16]
tags:
  - article/
---

# Mixed precision training

## Floating point numbers

* Neural networks require real numbers, which need to be represented in finite memory
* Single precision (FP32) is the default format with 4 bytes of storage:
	![[Pasted image 20240211115123.png]]


	- overflow => nan or $\pm \inf$
	- $\frac{\inf}{0} \implies$ nan

## Switching to lower precision
- faster performance 
- reduced memory usage (due to higher arithmetic intensity or smaller communication footprint)
- can use specialized hardware for even faster computation

**makes code prone to explode**

* naive FP16 is not only the option
* Specialized formats preserve dynamic range of computations:
	![[Pasted image 20240211120444.png]]
	- can have more bist for exponent and less for mantisse
- 

- FP16 exists since CUDA 8 - allocate the tensor/cast it to `half`. 
- BF16 on CPU and TPU - `Tensor.bfloat16()`. 
- TF32 is on demand on Ampere GPUs. 
	> Never exposed as a data type, only as a type for specific operations:
	> ![[Pasted image 20240211120723.png]]

### Tensor Cores
- Specialized computation units available in latest generations of Nvidia GPUs (since Volta)
- Allow the user to spped up $D = A \times B + C$ by up to 8-16x (claimed)
> ![[Pasted image 20240211121018.png]]
- Enabled not only for TF32/FP16/BF16 (Ampere), but even for INT8/INT4
- You **do not specify their usage manually** - to enable, need recent CUDA or specific cize constraints:
> ![[Pasted image 20240211121210.png]]
> ![[Pasted image 20240211121247.png]]


Check if optimization is used - run GPU profiler -` [i|s|h](\d)+` in kernel names. Also DL profiler can indicate Tensor Core eligibility and usage.

## Mixed precision training
- training in FP16 hardly works - some operations (matrix multiplication) can work, others (softmax, batch normalization) need higher precision
- **mix precision** training casts layer activations to appropriate data types. Supported in popular DL frameworks (`torch.cuda.amp`)
> ![[Pasted image 20240211121954.png]]



- Increases the training throughput due to the use of Tensor Cores
- Decreases the memory usage by half... ***or not***?

### Loss scaling
- with mixed precision, we need more bits. For gradients need more accuracy in mantisse or even exponent
- to prevent underflows (collapse to zero), we need to scale the FP16 gradients by a small number $\implies$ can lead to overflows when unscaling
- **Dynamic loss scaling** detects such overflows and repeatedly halves the scale
> ![[Pasted image 20240211122456.png]]


### Memory savings of AMP
- number of bytes per parameter for standard training with Adam:
	- FP32:
		- Parameter - 4 bytes
		- Gradients - 4 bytes
		- Optimizer statistics - 8 bytes
		**16 bytes in total**
	- AMP:
		- Master parameters in FP32 - 4 bytes
		- Parameters in forward pas - 2 bytes
		- Gradients - 2 bytes (sometimes 4)
		- Optimizer statistics - 8 bytes (very sensitive)
		**Also 16 bytes in total per parameter!**

- The only major savings come from reduced activation memory

## FP8 training
- On latest hardware (e.g. H100), we have even lower precision formats
- E4M3 is used for weights and activations, E5M2 is best for gradients
> ![[Pasted image 20240211124447.png]]

- Extra tricks (per-tensor scaling) required to maintain accuracy
> ![[Pasted image 20240211124544.png]]

## AMP: takeaways
- use more efficient data types when available
- Mind the sizes/operations types
- Don't expect significant memory savings for large models
- In many cases, this is easy to integrate through standard tools 

