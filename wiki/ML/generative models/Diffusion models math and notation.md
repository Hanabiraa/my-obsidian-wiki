---
created: [" 07-04-2023 23:26"]
aliases: []
tags:
- article/
---

# Basic notation and basic formula for predicting noise

* *$x_t$ - image after t iterations of noise
![[Pasted image 20230409103109.png]]

* *$x_T$ - final image (pure noise)

* *$q(x_t|x_{t-1})$. - forward diffusion process.
![[Pasted image 20230409103233.png]]
		it is normal distribution:
		![[Pasted image 20230409103458.png]]
		$\beta$ is bounded
* $p(x_{t-1}|x_t)$ - reverse process (denoising)
* additional notation:
	![[Pasted image 20230409103609.png]]

Parametrization trick:
![[Pasted image 20230409103636.png]]

## Forward dissusion process
**rewrite $q(x_t|x_{t-1})$**:
![[Pasted image 20230409103745.png]]

rewrite as initial distribution notation:
![[Pasted image 20230409103809.png]]

## Reverse diffusion process
![[Pasted image 20230409103914.png]]


![[Pasted image 20231213191440.png]]
- neural network gets noised image and timestamp $t$ and then predicts what should be removed from the image to make it cleaner

## Loss function
negative log-likelihood - compute variational lower bound
![[Pasted image 20231213191653.png]]
- teach neural network to predict the noise at any timestamp $t$

### Broader:
![[Pasted image 20230409104512.png]]

rewrite:
![[Pasted image 20230409104723.png]]

![[Pasted image 20230409104751.png]]
This is [[ELBO]] - first and third termes cancel each other

Can be rewritten as:
![[Pasted image 20230409104828.png]]

![[Pasted image 20230409104935.png]]
![[Pasted image 20230409105023.png]]

![[Pasted image 20230409105041.png]]

![[Pasted image 20230409105101.png]]

![[Pasted image 20230409105129.png]]
Simplify:
![[Pasted image 20230409105143.png]]
![[Pasted image 20230409105208.png]]

Ignore first term completely - can be certain that first term can be small

- forward process should be the same with reverse

![[Pasted image 20230409105426.png]]

* $\beta$ is fixed*
* derive $\mu$
![[Pasted image 20230409105518.png]]


![[Pasted image 20230409105539.png]]

Rewrite:
![[Pasted image 20230409105551.png]]

![[Pasted image 20230409105633.png]]
Mean squared error between noise and predicted noise

ignore scaling:
![[Pasted image 20230409105659.png]]


Reparameterize:
![[Pasted image 20230409105822.png]]

### For images:
![[Pasted image 20230409105905.png]]

![[Pasted image 20230409105923.png]]
* D - data dimension
* Integral - integrade over range of pixels

for each pixel calculate probability 
![[Pasted image 20230409110113.png]]

## Entire objective:
![[Pasted image 20230409110225.png]]


## Algorithm
![[Pasted image 20230409110254.png]]
![[Pasted image 20230409110303.png]]

at last step dont add noise


## Improvements
* learn interpolation of $\beta$
	 ![[Pasted image 20230409110459.png]]
* Better noise scheduler
	![[Pasted image 20230409110534.png]]
	![[Pasted image 20230409110544.png]]


# Math in diffusion models

## Connection to stochastic differential equations:
![[Pasted image 20231213192147.png]]


1) It describes how the distribution of samples change
![[Pasted image 20231213192236.png]]
2) also for the forward there si reverse SDE and ordinal=ry DE
![[Pasted image 20231213192312.png]]

- To obtain the image, integrate the reverse ODE
- deterministic encoding and generation means that neural network learns the same mapping between the noise and images every time

## Conspect from the lecture
В конспекте описывается связь диффузии с нейростохастическими диффурами

Основной смысл - есть процесс диффузии, он стохастический, но как вернуться обратно в ту же точку, если у нас есть случайность в процессе диффузии?
1) Запоминать все состояния и все случайные события - ОЧЕНЬ ПЛОХО
2) Вывести уравнение для стохастических диффуров такое, которое не зависит от случайности - **Уравнение Фокера-Планка**
	1) С его помощью можно понять, какое уравнение запустить, чтобы решить наше стохастическое обратно во времени
Также в конспекте разобрано, как перенести дискретные диффуры на напрерывный случай, как вернуться обратно во времени и откатиться к начальному состоянию (используются вышеописанные трюки)


И еще раз описан процесс обучения [[DDPM]], так как в статье была неполная информация

![[Diffusion models math-2.pdf]]