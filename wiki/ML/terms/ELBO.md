---
created: [" 07-04-2023 23:27"]
aliases: [Evidence lower bound, evidence lower bound]
tags:
- terms/
---

# ELBO

> Evidence lower bound

## definition
ELBO is useful lower bound of lig-likelihood of some observer data


We have:
* Hard-to-compute likelihood function $f(x)$
* Easy-to-compute function $g(x)<f(x) \  \forall x \implies g(x)$ can be ELBO
* by optimizing simpler objective (ELBO, $g(x)$), we are also sure that we optimize $f(x)$
![[Pasted image 20230409104558.png]]


## useful links
[[DDPM|Denoising diffusion probabilistic model]] 
[[Maximum likelihood estimation|MLE]]