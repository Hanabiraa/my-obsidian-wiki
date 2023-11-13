---
created:
  - " 08-11-2023 19:26"
aliases: 
tags:
  - article/
---

# Panoptic segmentation

##
> [[Semantic segmentation]] + [[Instance segmentation]]
- combine segmentation for stuff and things segmentation

![[Pasted image 20231108192733.png]]

### DETR
![[Pasted image 20231108192945.png]]

### Mask2Former

![[Pasted image 20231108193217.png]]

- teach to watch only on predicted region (in masked attention)
### OneFormer
![[Pasted image 20231108193439.png]]

**General review**:
![[Pasted image 20231108193714.png]]
- some feature pyramid and image features
- task is determined by prompt -> processed by MLP & transformer
- generate object queries

GT labels through text:
![[Pasted image 20231108193959.png]]
Encoded (contrastive loss):
![[Pasted image 20231108194047.png]]
- descriptions for different objects should be different
- for similar - should be similar semantically

