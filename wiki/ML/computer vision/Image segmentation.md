---
created:
  - " 08-11-2023 18:05"
aliases: 
tags:
  - article/
---

# Image segmentation

## [[Superpixels segmentation]]

## [[Semantic segmentation]]

## [[Interactive segmentation]]

## [[Instance segmentation]]

## [[Panoptic segmentation]]

## Human pose estimation
![[Pasted image 20231108194539.png]]

### regressin joint positions**:
![[Pasted image 20231108194713.png]]

### predicting joints using heatmaps
in some range (described by Gaussian): -> then get argmax
![[Pasted image 20231108194753.png]]
- N heatmaps for each point

### OpenPose
![[Pasted image 20231108195022.png]]
- Also predict affinity field to match limb to only one person
