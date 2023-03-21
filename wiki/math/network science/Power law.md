---
created: [" 21-03-2023 17:18"]
tags:
- article/
---

# Power law [[Degree distribution]]:
Take log
![[Pasted image 20230321160926.png]]

**Discrete power law distribution $\to$ Continuous approzimation:**

![[Pasted image 20230321161127.png]]

[[Hubs in networks]]


### Moments of power-Law distribution
![[Pasted image 20230321162247.png]]
- $\gamma > 2$ - exists first moment
- $\gamma > 3$ - exists second moment
- $\gamma > m + 1$ -  exists m-th moment
**- something strange happens in $3 < \gamma < 3$ ($\alpha$ is $\gamma$ in a graph):**
	![[Pasted image 20230321170102.png]]
	- \<k>  stays the  same
	![[Pasted image 20230321170647.png]]
	- there is a scale that exists when $\gamma > 3 \ \to$ **there is no particular size of [[Node degree]] is common for all nodes in a graph**

## Properties of scale-free networks
![[Pasted image 20230321171008.png]]
have 3 regions of $\gamma$
- $\gamma < 2$ - maximum node degree grows faster than upper bound - impossible

### parameter estimation of $\gamma$
- MLE:
	![[Pasted image 20230321171415.png]]
	get log-likelihood:
	![[Pasted image 20230321171456.png]]


where to start a line - Kolmogorov-Smirnov test:
![[Pasted image 20230321171603.png]]
**- select point from which to start mapping the line**
