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
**Idea:** generate top-k eigenvectors/values first and use them to **condition the graph generation**

![[Pasted image 20230416162157.png]]


