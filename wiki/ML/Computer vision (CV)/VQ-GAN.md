---
created:
  - " 13-12-2023 18:52"
aliases: 
tags:
  - article/
---

# VQ-GAN

## Overall structure

![[Pasted image 20231213185252.png]]

- encode image and get features
- obtain hidden discrete distribution of features
- also have discriminator, which watches image patches and predicts whether it's fake or real $\to$ average and get loss
- also have transformer for attentive context generation


![[Pasted image 20231213185624.png]]


#### Can work in both conditional and unconditional regime

**Conditional regime** - have some tokens which will direct the generation process
**Unconditional** - just generation