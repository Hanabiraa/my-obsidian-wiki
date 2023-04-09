---
created: [" 09-04-2023 11:10"]
aliases: [Self attention and cross attention]
tags:
- article/
---

# Cross-attention

Used in stable diffusion too

##  Self attention
![[Pasted image 20230409111119.png]]
* Q, K, V - 3 matrices

Self attention on image vs. convolutiom

![[Pasted image 20230409111208.png

* reshape image:
![[Pasted image 20230409111254.png]]

* get Q, K, V of tensor
		Projection:
		![[Pasted image 20230409111339.png]]

	![[Pasted image 20230409111401.png]]
$QK^T$ - similarity of each pixel to each pixel (similarity of vectors)
$\sqrt{d}$ - stabilization constant (d - dimension)

$softmax$ - how each pixels are related (bound in [0,1])
$softmax(...)V$ - get embeddings of each pixel according to similarity to another pixels and its own embedding in V
![[Pasted image 20230409112639.png]]

then project attention to initial dimension + add [[Skip connection]] (add attention again)

## Cross-attention

- Only Q is projection of initial image
- K and V are projections of prompts (captions)

![[Pasted image 20230409112216.png]]
![[Pasted image 20230409112323.png]]

