---
created:
  - " 22-11-2023 18:07"
aliases: 
tags:
  - article/
---

# Self-supervised learning

## Motivation - labelling tradeoff
- labeling enough data is exoensive
- labels always have errors
- training people to label effectively and without many errors is hard
- lots of unlabelled data exists (e.g. video), can we use it somehow?

![[Pasted image 20231122180822.png]]

## Self-supervised pretraining
Pipeline:

![[Pasted image 20231122181544.png]]

(pretext) - отговорка

### Pretext tasks
 ### Context prediction (e.g.)
- predict context lcoaliation of an image

![[Pasted image 20231122181757.png]]

![[Pasted image 20231122182422.png]]

- **Are hard to come up with**
- **Hard to predict whether learned features will be general enough**
## Contrastive learning

- augmentations don't change the content of an image => features should be same for augmented and anchor image
- also there is negative sample (another image not related to anchor one)

![[Pasted image 20231122182641.png]]

![[Pasted image 20231122182904.png]]


### SimCLR

- Cosine similarity as the score function:
![[Pasted image 20231122183208.png]]

![[Pasted image 20231122183802.png]]

**Need VERY large batch**

### MoCo


## Foundation models
> model which works with multiple modalities (i.e. text and images)
> solves several tasks in different domains (i.e. classification. segmentation, question answering)
> promptable, i.e. supports several types of queries regarding analyzed information
> works well without finetuning


### CLIP
![[Pasted image 20231122192616.png]]

- have LARGE noisy dataset of images, run images and text through image encoder and text encoders
- compare resulting features and make pairs from the same image-text pair similar

After that:
use these encoders for some specific tasks


