---
created: [" 21-03-2023 17:54"]
aliases: [BA, Barabasi_Albert_model]
tags:
- article/
---

# Preferential attachment graph model

## Definition

![[Pasted image 20230321175521.png]]
- prefers to connect to the nodes with more connections

**Different t**:
![[Pasted image 20230321175637.png]]

### Continuous approximation
evolving degree of a node i after some time

- new nodes adds m edges
![[Pasted image 20230321175810.png]]

- degree of a node grows as sqrt

![[Pasted image 20230321175919.png]]

degrees of older nodes will always have higher [[Node degree]] than newr ones

## CDF of distribution
![[Pasted image 20230321180106.png]]


## Attachment uniformly at random
![[Pasted image 20230321180204.png]]

- node degree will grow slower
- CDF is exponential
**Preferential attachment is critical for [[Power law]] distribution**


## Average path length, clustering coefficient:
![[Pasted image 20230321180327.png]]


# Non-linear PA

- proportional to some power
![[Pasted image 20230411005808.png]]


# Copying model

### Select next nodes - get scale-free graph
![[Pasted image 20230321180536.png]]

