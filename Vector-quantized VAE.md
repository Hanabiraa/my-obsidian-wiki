---
created:
  - " 13-12-2023 18:32"
aliases: 
tags:
  - article/
---

# Vector-quantized VAE

## Overall pipeline

![[Pasted image 20231213183313.png]]



- e.g. resnet for encoder
- search the most similar feature in the embedding dict (learned)
- map the index for each feature the index of the most similar embedding (**discrete distribution**)