---
created: [" 12-04-2023 17:24"]
aliases: [Digress, discrete diffusion, Discrete diffusion]
tags:
- article/
- graph_generation
---

# Discrete denoising diffusion for [[Graph]] generation

Link- [Original video](https://www.youtube.com/watch?v=k2saMtP-Fn8)

## [[DDPM|Denoising diffusion probabilistic model]] - refreshing

- Noise model -> Denoising network
- diffusion trajectories

1. Sample noise
2. Apply denosing
3. Sample from this distribution
4. Iterate to step 1


## Discrete diffusion
![[Pasted image 20230412173253.png]]
**explain:**
- noise model - Markov process represented as transition matrix
- multiple steps is just a product
- probability of jumping to another state is independent from previous
- **discrete space!** - sample one element from the probability

## Digress
- have database of graphs (X, E) - node and edge attributes
- goal is to generate new graphs that *look* like train graphs
- Unconditional generation is a prerequisite for most complex tasks

#### Training
- start from clean graph G
- noise it (G_T)
- **noise is independent**
- Q - transition matrix
- prediction of every edge in a graph and every node in a graph

![[Pasted image 20230412174244.png]]


![[Pasted image 20230413162851.png]]


#### Sampling
- start from random graph - prior distribution that we start from should be the limit distribution of our noise
- number of nodes doesn't change
![[Pasted image 20230412174420.png]]

### Denoising network

- want to predict edge features
- use graph [[Transformer]] layers - incorporate info from edges
![[Pasted image 20230413151740.png]]

**transformer layer:**
![[Pasted image 20230413151758.png]]

- PNA - 
- FiLM - 

**Permutation equivariance**
- because graph can be represented by $n!$ adjacency matrices
- Digress is build with permutation equivariant blocks
- All permutations of a generated graph are equally likely generated
- Loss is premutation invariant too:
	![[Pasted image 20230413152155.png]]

- **need sparse graph - may take many iterations**

### Improving DiGress

- start from uniform transition matrix - problem with sparsity
- choose a noise model that preserves the marginal distribution of node and edge types  at every step $\implies$ probability of jumping from $i$ to $j$ is proportional to marginal distribution of $j$ in the training set
	![[Pasted image 20230413152614.png]]
	(*e.g.: suppose in the training set we have graphs with 0.8 sparsity, 0.1 edges are type $a$, 0.1 edges are type $b$ $\implies$ probability of jumping from any state to no-edge is 8 times bigger then prob of jumping from any state to type $a$ of edge*)

- different transition matrix at different $t$
- $\alpha_t$, $\beta_t$ - cosine scale
- $m^{`}_X$ and $m^{`}_X$ are computed during training

**result:**
![[Pasted image 20230413152720.png]]

### Structural and spectral features

*Problem* - [[Message-passing neural networks]] have limited representation power and cannot detect substructures such as cycles
*Solutions*
1. Use more expressive net -> *costly* in practise
2. Add features that the **network cannot compute by itself**
	1. Cycle counts
	2. Spectral features (# 0 eigenvalues, first nonzero eigenvalues, first eigenvalulues)

Computed on noisy graphs as they are discrete

![[Pasted image 20230413153405.png]]

### Algorithm of DiGress

**Training**
1. Sample graph
2. Sample timestep
3. add noise 
4. Compute structural and spectral features of a noisy graph
5. Give noisy graph and features to the denoising network
6. Compute [[Cross-Entropy loss]]
![[Pasted image 20230413153650.png]]

**Sampling**
1. Sample number of nodes
2. Sample random graph
3. At each iteration
	1. Compute the features
	2. Predict the clean graph
	3. Combine the distributions of edges and nodes of clean graph and samples from them
4. Sample
![[Pasted image 20230413153953.png]]

### Evaluation
- compare properties of generated graphs with graphs from dataset
	- Validity
	- Uniqueness
	- Novelty

### Discrete regression guidance
![[Pasted image 20230413155031.png]]

#### Sampling with discrete guidance
![[Pasted image 20230413155133.png]]
