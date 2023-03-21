---
created: [" 21-03-2023 17:34"]
aliases: [phase_transition]
tags:
- article/
---

# Phase transition in a [[Erdos-Renyi graph model|ER]] graph model as an example

## Derivation

u - fraction of nodes not belonging to the gigantica connected component
s - size of GCC


- its either disconnected node
- either connected to 1 node which is not in GCC
- .... k whic is not in GCC
- ... see formula:
![[Pasted image 20230321173708.png]]

- this is equation
- $\lambda$ is [[average node degree]]

### Graphical analysis of the equation:
(c are $\lambda$)
![[Pasted image 20230321174043.png]]

						$\implies$ nonzero solution exists when derivative is greater than 1


## GCC starts appearing when $\lambda = 1 \implies$ when every node on average has 1 neighbor


### Summary:
![[Pasted image 20230321174442.png]]


### Numerical simulation
phase transition at $\lambda = 1$
![[Pasted image 20230321174703.png]]