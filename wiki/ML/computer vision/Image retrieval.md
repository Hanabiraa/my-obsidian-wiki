---
created:
  - " 25-10-2023 18:11"
aliases: 
tags:
  - article/
---

# Image retrieval

## Examples of a task
- **verification**:
![[Pasted image 20231025181201.png]]
- **identification**
	- given a database of images and a single query image, determine of the person in the query image is present in the database
	- can be reduced to $N$ pairwise verification tasks (*one-/few-show classification*)
![[Pasted image 20231025181815.png]]
- **Near-duplicates**:
	- asd
![[Pasted image 20231025182433.png]]

## overall Pipeline

1) Train image descriptors
	1) Image $I$ is fed to neural network - classification task -> then cut head of NN and leave only last layers, which will produce descriptors
2) Construct image index
	1) Feed $I_k$ to neural network and then obtain set of features
	2) Store it in data stracture [NN] for example
3) Search query in index
	1) $I_q$ query -> Neural network -> $f_{\theta q}$ -> to index

### Datasets
1) **Oxford 5k**
- 5k images with Oxford landmarks
- distratctor images
- ![[Pasted image 20231025183102.png]]
2) **Google landmarks**
- 762k index images, 4.1M train images, 200k landmarks
- 118k images for test
![[Pasted image 20231025183256.png]]

### Metrics & Losses

#### Recall@R quality metric
![[Pasted image 20231025183632.png]]

#### Contrastive loss
![[Pasted image 20231025184123.png]]
- If labels are the same, it minimizes the distance between two images
- If labels are different, pushes dots unitl they are at least at $\alpha$ distance
#### Triplet Loss
![[Pasted image 20231025184339.png]]
- How to define triplets?
- unstable
- difficulties with distributed learning
#### Center Loss
![[Pasted image 20231025184551.png]]
- tries to distinguish clusters - additional term in the loss which measures distance to centroids to each class
- update and train centroids

#### SphereLoss

![[Pasted image 20231025185318.png]]
- projects dots on a circle, can separate classes


#### CosFace, ArcFace
![[Pasted image 20231025185454.png]]


### Efficient searching


#### Inverted index
![[Pasted image 20231025190512.png]]
- unoptimal, NP-hard
- K centroids form codebook
- for each centroid, store images according to each centroid in RAM (16 byte/object), then search among them

**Drawbacks**
- can't use for large collections


#### Product quantization

![[Pasted image 20231025191039.png]]

- K-means for subvectors

Distance between vectors:
![[Pasted image 20231025191340.png]]

#### Hierarchical Navigable Small world
![[Pasted image 20231025191713.png]]
