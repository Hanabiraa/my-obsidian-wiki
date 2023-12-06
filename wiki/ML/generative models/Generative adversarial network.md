---
created: [" 09-04-2023 12:50"]
aliases: [GAN]
tags:
- article/
---

# Generative adversarial network

## Definition
- Based on game theory min-max game - discriminator and a generator compete against each other
- The generator generates data from stochastic noise, and the discriminator tries to tell whether it is real (coming from a training set) or fabricated (from the generator). 
- The absolute difference between carefully calculated rewards from both networks is minimized so that both networks learn simultaneously as they try to outperform each other.

2 models:
1) generative model Gθ which captures the data distribution p(x)
	1) To learn a generator distribution $p_{model}(x)$ of observed data x, the generator builds a mapping function from a prior noise distribution pz(z) to data space as Gθ(z).
2) discriminative model Dφ which estimates the probability that a sample comes from the training set rather than Gθ
	1) discriminator, Dφ(x), outputs a single scalar representing the probability that the input data x came form the training data rather than sampled from $p_{model}(x)$
3) The generator and discriminator are both trained simultaneously by adjusting the parameters of pmodel(x) to minimize log(1 − Dφ(Gθ(z)) and adjusting the parameters of Dφ to minimize logDφ(x), as if they are following the two-player min-max game with value function V (Gθ, Dφ):
	![[Pasted image 20230409131245.png]]

The training of the generator and discriminator is kept alternating until the generator can hopefully generate reallike data that is difficult to discriminate from real samples by a strong discriminator.



## Picture of workflow
![[Pasted image 20230409125143.png]]

## Key points
* In general, GANs show great power in generating data such as image, audio, and texts.
* In contrast to [[Variational autoencoder|VAE]], GANs learn to generate samples without assuming an approximate distribution. 
* By utilizing the discriminator, GANs avoid optimizing the explicit likelihood loss function, which may explain their ability to produce high-quality objects. 
* However, GANs still have **drawbacks**. 
	* One is that they can sometimes be extremely hard to train in adversarial style. They may fall into the divergence trap very easily by getting stuck in a poor local minimum. 
	* Mode collapse is also an issue, where the **generator** **produces samples that belong to a limited set of modes**, which results in **low diversity**. 
	* Moreover, alternatively training and large computation workloads for two networks can result in long-term convergence process


## Training

По очереди

![[Pasted image 20231206212912.png]]

![[Pasted image 20231206212931.png]]


