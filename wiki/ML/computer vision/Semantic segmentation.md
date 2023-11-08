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
- increase convolution - transposed convolution
![[Pasted image 20231108182336.png]]
- can initialize kernel by ourselves -thus, if it's frozen, it's similar to bilinear interpolation.