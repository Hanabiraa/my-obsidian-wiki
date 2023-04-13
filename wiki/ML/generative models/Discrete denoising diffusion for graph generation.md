---
created: [" 12-04-2023 17:24"]
aliases: [Digress, discrete diffusion, Discrete diffusion]
tags:
- article/
---

# Discrete denoising diffusion for [[Graph]] generation

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

#### Sampling
- start from random graph - prior distribution that we start from should be the limit distribution of our noise
- number of nodes doesn't change
![[Pasted image 20230412174420.png]]

### Denoising network

- want to predict edge features
- use graph [[transformer]] layers - incorporate info from edges
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

#### Improving DiGress

- start from uniform transition matrix - problem with sparsity
- choose a noise model that preserves the marginal distribution of node and edge types  at every step $\implies$ probability of jumping from $i$ to $j$ is proportional to marginal distribution of $j$ in the training set
	![[Pasted image 20230413152614.png]]

**result:**
![[Pasted image 20230413152720.png]]
