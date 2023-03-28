---
created: [" 22-03-2023 14:33"]
aliases: []
tags:
- article/
---

# Stochastic block model
Is a generative model for random graphs. Tends to produce graphs with given size of communities


## Parameters
* $n$ - number of vertices
* Partition of the vertex set ![\{1,\ldots,n\}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0401c38cf1a2e51b30b38f4b93b5285aa77f8fad) into disjoint subsets ![C_1,\ldots,C_r](https://wikimedia.org/api/rest_v1/media/math/render/svg/ea48991a0db877bf2b03adda419a85fb67aa80bb), called _communities_
* a symmetric $r \times r$ matrix $P$ of edge probabilities

The edge set is then sampled at random as follows: any two vertices i![u \in C_i](https://wikimedia.org/api/rest_v1/media/math/render/svg/57e4e25b2cbc01159fe6286abc7690a1eaa10d7d) and ![v \in C_j](https://wikimedia.org/api/rest_v1/media/math/render/svg/2da0495637390b212d04c4bc76004e7d3329edb3) are connected by an edge with probability ![P_{ij}](https://wikimedia.org/api/rest_v1/media/math/render/svg/43ef37c239b6d38f1e951a31eb1a3bd295271b40). An example problem is: given a graph with  ![n](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) vertices, where the edges are sampled as described, recover the groups ![C_1,\ldots,C_r](https://wikimedia.org/api/rest_v1/media/math/render/svg/ea48991a0db877bf2b03adda419a85fb67aa80bb).
### Special case - constant matrix
* If the probability matrix is a constant, in the sense that ![P_{ij} = p](https://wikimedia.org/api/rest_v1/media/math/render/svg/4e88adf8ec84bf231ed001abcc3eb88ffa30bb30) for all ![i,j](https://wikimedia.org/api/rest_v1/media/math/render/svg/f4cbf8bbc622154cda8208d6e339495fe16a1f9a), then the result is the [[Erdos-Renyi graph model]] e![G(n,p)](https://wikimedia.org/api/rest_v1/media/math/render/svg/ad8d6ba8bbe18701bed34c2d5106de6a56e35e08). 
* This case is degenerate—the partition into communities becomes irrelevant—but it illustrates a close relationship to the Erdős–Rényi model.

### Planted partition model
* The _planted partition model_ is the special case that the values of the probability matrix ![P](https://wikimedia.org/api/rest_v1/media/math/render/svg/b4dc73bf40314945ff376bd363916a738548d40a) are a constant ![p](https://wikimedia.org/api/rest_v1/media/math/render/svg/81eac1e205430d1f40810df36a0edffdc367af36) on the diagonal and another constant ![q](https://wikimedia.org/api/rest_v1/media/math/render/svg/06809d64fa7c817ffc7e323f85997f783dbdf71d) off the diagonal. 
* Thus two vertices **within the same community** share an edge with probability ![p](https://wikimedia.org/api/rest_v1/media/math/render/svg/81eac1e205430d1f40810df36a0edffdc367af36), while two vertices **in different communities** share an edge with probability q![q](https://wikimedia.org/api/rest_v1/media/math/render/svg/06809d64fa7c817ffc7e323f85997f783dbdf71d). 
* Sometimes it is this restricted model that is called the stochastic block model. 
* The case where ![p > q](https://wikimedia.org/api/rest_v1/media/math/render/svg/3988956bbb7d322230b1aedcf7c5e3121da6edf4) is called an [[Assortative model]], while the case ![p<q](https://wikimedia.org/api/rest_v1/media/math/render/svg/9f86c7ea4068f76f93c6a2a92c849c27303c9ba9) is called [[Disassortative model]].


### Strongly and weakly [[Assortative model]] 
* A model is called _strongly assortative_ if ![P_{ii} > P_{jk}](https://wikimedia.org/api/rest_v1/media/math/render/svg/32ab8b73e3f0a52e4c31ac79c41f11b29534085c)whenever ![j \neq k](https://wikimedia.org/api/rest_v1/media/math/render/svg/a08a8f7e7c65621ea80f6989770e39aa591d0886): **all diagonal entries dominate all off-diagonal entries**
* A model is called _weakly assortative_ if ![P_{ii} > P_{ij}](https://wikimedia.org/api/rest_v1/media/math/render/svg/bf7f7890d7deb67f517b74404eb9003dcd64d84e) whenever ![i\neq j](https://wikimedia.org/api/rest_v1/media/math/render/svg/d95aeb406bb427ac96806bc00c30c91d31b858be): **each diagonal entry is only required to dominate the rest of its own row and column**.
* _Disassortative_ forms of this terminology exist, by reversing all inequalities. For some algorithms, recovery might be easier for block models with assortative or disassortative conditions of this form