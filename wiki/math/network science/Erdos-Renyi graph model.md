---
created: [" 21-03-2023 17:22"]
aliases: [ER, ER_model, random_graph_model]
tags:
- article/
---

# Erdos-Renyi graph model


## Basics
- have n nodes, $N = \frac{n(n-1)}{2}$ pairs of nodes
- each pair is connected with probability $p$

Average number of edges in a graph:
$<m> = p \frac{n(n-1)}{2}$

Average [[Node degree]]:
![[Pasted image 20230321172740.png]]

[[Graph density]]:
![[Pasted image 20230321172914.png]]

Probability of a network to have m edges is given by Bionomial distribution
![[Pasted image 20230321173121.png]]

### [[Local clustering coefficient|clustering-coefficient]]:

![[Pasted image 20230321175008.png]]
- si equal to the probability that 2 nodes are connected

### [[Graph diameter]]
![[Pasted image 20230321175126.png]]

Graph of average graph distance for some models:
![[Pasted image 20230321175227.png]]
random network posess small-world property


### Average path length and summary of another properties
![[Pasted image 20230321175309.png]]



## [[Degree distribution]]
- binomial distribution
- probability that i-th node has degree $k_i=k$ is given by bionimial distribution
![[Pasted image 20230321173250.png]]
![[Pasted image 20230321173351.png]]


when p starts growing, [[Phase transition]] occurs - suddenly giant connected components start to occur in a graph:
![[Pasted image 20230321173551.png]]


## Evolution of random network:
![[Pasted image 20230321174521.png]]

## Threshold probabilities
- can predict at which p start getting different structures
![[Pasted image 20230321174801.png]]

