---
created:
  - " 08-11-2023 18:16"
aliases: 
tags:
  - article/
---

# Semantic segmentation

> for each object on an image, we know the label - predict

For instance, `Cityscapes` dataset:
![[Pasted image 20231108181750.png]]

- Costly annotation. 90 minutes on an image, 5-10\$/hr...
- Some coarse annotation - for additional regularization\

## models and key ideas

### DeepLab
DCNN - deep convolutional neural network (AlexNet or VGG)
![[Pasted image 20231108182113.png]]
- after DCNN, get score map (heat map)
- enchance details - B-linear interpolation
- apply Fully connected CRF (some graph model which simulates all pixels interactions)
![[Pasted image 20231108182247.png]]


### Fully convolutional networks

- have some backbone
- get features
- increase resolution - transposed convolution - upsample
![[Pasted image 20231108182336.png]]
- can initialize kernel by ourselves -thus, if it's frozen, it's similar to bilinear interpolation.
- somewhat similar to feature pyramids

### Segnet with unpooling
![[Pasted image 20231108182636.png]]
- encoder & decoder mirrored architecture

### DeconvNet
collapse and uncollapse with convolution

### U-net
- can also transfer features
- initiallly used for cells segmentation in medicine
![[Pasted image 20231108182919.png]]

### Hourglass networks (песочные часы)

![[Pasted image 20231108183216.png]]

Can even stack them (and use skip connections)
![[Pasted image 20231108183233.png]]

### Atrous convolutions (dilated)
- ordinary convolution collects info from some a small portion of an image
- have convolution with dilated distance from each element
![[Pasted image 20231108183545.png]]

Backbone changes - can lose information with ordinary convolution. With dilated, we don't lose much of an image size
![[Pasted image 20231108183649.png]]


Can combine and have Atrous pyramid:
![[Pasted image 20231108183820.png]]


### HRNet
- there is no standard backbone 
- parallel tensor computation of original and some downsampled tensor
- reduce resolution
- aggregate information from different resolution sizes

![[Pasted image 20231108184111.png]]


## Transformers!

### SegFormer
![[Pasted image 20231108184830.png]]
- Similar to SWin architecture
- overlap patch embeddings - chunk picture on overlapping patches

Efficient Self-atttention:
- merge neighboring tokens
![[Pasted image 20231108184922.png]]
- mix FFN - Convolution of MLP result - aggregate information from neighboring tokens, followed by another convolution
- Don't need to encode positional information, b.c. convolution in Mix-FFN does it

**Decoder:**
- Upsample after MLP - works, because:
	- all features are more high-level => can upsample faster
- MLP and classification of n classes

