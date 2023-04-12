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
- noise model - markov process represented as transition matrix
- multiple steps is just a product
- probability of jumping to another state is independent from previous

## Digress
- have database of graphs (X, E) - node and edge attributes
- goal is to generate new graphs that *look* like train graphs
- Unconditional generation is a prerequisite for most complex tasks

