---
created: [" 09-04-2023 12:28"]
aliases: [VAE]
tags:
- article/
---

# Variational autoencoder

## Definition of work
- VAE is a latent variable-based model that pairs a top-down generator with a bottom-up inference network. 
- Instead of directly performing maximum likelihood estimation on the intractable marginal log-likelihood, training is done by optimizing the tractable [[ELBO|evidence lower bound]]:

![[Pasted image 20230421223525.png]]

- Suppose we have a dataset of samples $x$ from a distribution parameterized by ground truth generative latent codes $z \in \mathbf{R}^c$ ( c refers to the length of the latent codes). 
- VAE aims to learn a joint distribution between the latent space z ∼ p(z) and the input space x ∼ p(x). 
- Specifically, in the probabilistic setting of a VAE, the encoder is defined by a variational posterior $q_{\phi}(z|x)$, 
- while the decoder is defined by a generative distribution $p_{\theta}(x|z)$, as represented by the two orange trapezoids in [[Pasted image 20230409123459.png]]
- $\phi$, $\theta$ are trainable parameters of the encoder and decoder. 
- The VAE aims to learn a marginal likelihood of the data in a generative process as: 
	![[Pasted image 20230409124139.png]]
	
Then the marginal likelihoods of individual data points can be rewritten as follows:
![[Pasted image 20230409124203.png]]

* First term - [[KL-Divergence]] between true and the approximate posterior
* Second term - [[ELBO]] on the marginal likelihood

Thus, maximizing L(φ, θ; x, z) is to maximize the lower bound of the true objective L(φ, θ; x, z) = Eqφ(z|x)[logpθ(x|z)] − DKL(qφ(z|x)||p(z))

- In order to make the optimization of the above objective tractable in practice, we assume a simple prior distribution **p(z) as a standard Gaussian N (0, I)** with a diagonal covariance matrix. 
- Parameterizing the distributions in this way allows for the use of the “reparameterization trick” to estimate gradients of the lower bound with respect to the parameter φ, where each random variable $z_i \sim q_{\phi}(z_i|x)$ is parameterized as Gaussian with a differentiable transformation of a noise variable  ∼ N (0, 1), that is, z is computed as:
		![[Pasted image 20230409124702.png]]
where μ and σ are outputs from the encoder.

## Picture of VAE framework
![[Pasted image 20230409123459.png]]