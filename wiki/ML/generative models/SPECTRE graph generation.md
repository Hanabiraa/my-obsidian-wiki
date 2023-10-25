---
created: [" 16-04-2023 16:09"]
aliases: []
tags:
- article/
- graph_generation
---

# SPECTRE graph generation

## Problems with one-shot generation
- need to control the global graph structure by local interactions
	- harder and harder as graph becomes larger
- generate only small graphs

## Take inspiration from spectral graph theory
**Idea:** generate top-k eigenvectors/values of **normalised laplacian** first and use them to **condition the graph generation**

![[Pasted image 20230416162157.png]]

### Work of [[Generative adversarial network|GAN]] inside
![[Pasted image 20230417184212.png]]

- $k$ is the number of top k eigenvalues and eigenvectors pairs, $n$ is the number of nodes
- 3 different generators and estimators
	- A - Adjacency generation
	- $\lambda$ - eigenvalues generation
	- $U$ - eigenvectors generation

- Sets are samples from normal distribution
- *normalise to the unit length the inputs of MLP*
- eigenvectors are orthogonal


#### $g_U$ - eigenvector generator
![[Pasted image 20230416165426.png]]

top k eigenvectors lie on some manifold (*Stiefel manifold*)

![[Pasted image 20230416165622.png]]
- generate rotation matrices
- apply to some vectors which could be possibly eigenvectors ti improve
- ==> move across this Stiefel manifold by multiplying on rotation matrices
- 3 layers don't share weights

### Dealing with cospectral graphs - nothing explicit

### Problem to check whether $U^{(k)}$ can produce graph or not

