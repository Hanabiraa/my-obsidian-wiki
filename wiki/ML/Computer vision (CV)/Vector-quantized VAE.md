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
- map the index for each feature the index of the most similar embedding 
- **discrete distribution** $\implies$ can be thought as quantization


###  Loss:
![[Pasted image 20231213183753.png]]

- reconstruction loss
- $e$ - closest feature vector embedding

## VQ-VAE 2

Main idea: make generation sequential:

![[Pasted image 20231213184633.png]]

- have 2 VAE
	- first predict features in low resolution
	- second predicts in the bigger dimension

