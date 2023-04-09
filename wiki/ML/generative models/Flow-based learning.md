---
created: [" 09-04-2023 13:24"]
aliases: [Autoregressive flow]
tags:
- article/
---

# Flow-based learning

## Model idea and formalization

- Normalizing flows (NFs) are a class of generative models that **define a parameterized invertible deterministic transformation between two spaces** z and x. 
	- z ∼ pz(z) is a latent space that follows distribution such as Gaussian, and x ∼ px(x) is a real-world observational space of objects such as images, graphs, and texts.
- Let fθ : z  → x be an invertible transformation parameterized by θ. Then the relationship between the density function of real-world data x and that of z can be expressed via the change-of-variables formula:
	![[Pasted image 20230409132626.png]]
- There are two key processes of normalizing flows as a generative model:
	1) **Calculating data likelihood**: given a datapoint x, the exact density px(x) can be calculated by inverting the transformation $z=f^{-1}_{\theta}(x)$
	2) **Sampling**: x can be sampled from the distribution px(x) by first sampling z ∼ pz(z) and then performing the transformation x = fθ(z). 
- To efficiently perform the above mentioned operations, fθ is required to be invertible with an easily computable Jacobian determinant.

## Autoregressive flow (AF)
> is a variant of normalizing flow by providing an easily computable triangular Jacobian determinant. It is specially designed for modeling the conditional distributions in the sequence.

- Formally, given $x \in \mathbf{R}^D$ (D is the dimension of observed sequential data), the autoregressive conditional probabilities for the d-th element in the sequence can be parameterized as Gaussian:
	![[Pasted image 20230409132941.png]]
	where μd = g_θ(x1:d−1) and σ_d = g_φ(x1:d−1) (gθ and gφ are unconstrained and positive scalar functions of $x_{1:d-1}$ respectively for computing the mean and deviation). In practice, these **functions can be implemented as neural networks**.
- The affine transformation of AF can be written as follows:
	![[Pasted image 20230409133123.png]]
	where z_d is the randomly sampled value from standard Gaussian. 
- The **Jacobian matrix here is triangular**, since ∂xi/∂zj is non-zero only for $j \le i$.
- Therefore, the determinant can be efficiently computed through $\prod_{d=1}^D\sigma_d$
- Specifically, to perform density estimation, we can apply all individual scalar affine transformations in parallel to compute the base density, each of which depends on previous variables $x_{1:d-1}$ 
- to **sample x**, we can first sample $z \in \mathbf{R}^D$ and compute $x_1$ through the affine transformation, and then each subsequent $x_d$ can be computed sequentially based on $x_{1:d-1}$ 