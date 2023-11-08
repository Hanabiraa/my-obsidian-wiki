---
created:
  - " 08-11-2023 18:07"
aliases: 
tags:
  - article/
---

# Superpixels segmentation

## Overview

![[Pasted image 20231108180724.png]]

- Unsupervised
- even without neural networkk

Why do we need superpixels? They are like generalizations of pixels - good for apriori information for neural network

> In 2D- superpixels, in 3D - supervoxels

## Algorirthm
### SLIC (Simple Linear Iterativ Clustering)
![[Pasted image 20231108181101.png]]

- CIELAB - euclidean distance correlates with human perception of color difference
- Centers are at distance $S$
- Stop - when clusters stabilise or after maximum iterations
- Linear dependence on number of pixels and number of clusters

![[Pasted image 20231108181524.png]]

Other methods are not so good. And TurboPixel is sloooow.....:
![[Pasted image 20231108181544.png]]