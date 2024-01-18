---
created:
  - " 18-01-2024 18:27"
aliases: 
tags:
  - article/
---

# GPU architecture
![[Pasted image 20240118182857.png]]

![[Pasted image 20240118183226.png]]

- more parallel cores

## CUDA computation model

![[Pasted image 20240118183514.png]]


### Hardware side:

![[Pasted image 20240118183811.png]]

## Why does all of this matter?

- The most popular operation is matrix multiplication - need to split a matrix on blocks to execute properly
![[Pasted image 20240118185013.png]]
**tiled quantization** - some of the thread blocks are note used at full

![[Pasted image 20240118184639.png]]

**wave quantization** - GPU executes very few operations in sec, because some wave (iteration of threadblocks) doesn't use all power

- if wave size is less then number of threadblocks on SMT - less speed
![[Pasted image 20240118184859.png]]


## Memory access

![[Pasted image 20240118185357.png]]

The speed is often determined by how fast we can transfer data to GPU

pin memory ("reserve" RAM space for torch data transfer) can cause instability of a system

GPU memory hierarchy:
![[Pasted image 20240118185737.png]]

## Asynchronous execution

![[Pasted image 20240118185956.png]]

- if code can go further without waiting for the result - it goes

### DL specifics

![[Pasted image 20240118190242.png]]