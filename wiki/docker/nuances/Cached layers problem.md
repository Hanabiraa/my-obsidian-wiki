---
created: [" 27-03-2023 23:01"]
aliases: []
tags:
- article/
---

# Cached layers problem

Detailed explanation in this [[Docker best practices#Minimize the Number of Layers|article part - Minimize the Number of Layers]]

## When using cached layers

- First case - layer with update is cached
- Second case
![[Pasted image 20230327230249.png]]

![[Pasted image 20230327230454.png]]

- in the second case, whenever our files are changed, docker will not install each dependencies at each time we build container, and will use cached layer, which improves speed of container building