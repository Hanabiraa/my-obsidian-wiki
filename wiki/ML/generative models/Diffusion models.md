---
created: [" 07-04-2023 18:08"]
aliases: [Диффузионная модель, DALLE-2]
tags:
- article/
---

# Diffusion models basics


## Basic idea of diffusion model:

1) Apply random noise to object sequentially (noises have Normal distribution)
![[Pasted image 20230409102844.png]]
2) Try to restore image from noise thus learning latent distributuion - **reverse diffusion process** - done by neural network gradually
![[Pasted image 20230409102944.png]]



# More broadly in the following articles:

## [[Energy-based models]] и Noise condition score network  - closely related to diffusion models


## [[DDPM|Denoising diffusion probabilistic model]] - SOTA in diffusion models


## Жоская [[Diffusion models math and notation]], стоящая за диффузионками

# High-res conditional generation 

Conditional sampling can be achieved using three methods:
![[Pasted image 20231213192841.png]]


![[Pasted image 20231213193256.png]]

# DALL-E 2
![[Pasted image 20231213193611.png]]
