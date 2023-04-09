---
created: [" 09-04-2023 13:35"]
aliases: [RL, DQN]
tags:
- article/
---

# Reinforcement Learning and Deep Q-Network

## RL

> Reinforcement learning (RL) is a commonly used framework for learning controlling policies by a computer algorithm, the so-called **agent**, through interacting with its environment

- in RL process, an agent is faced with a sequential decision making problem, where interaction with the environment takes place at discrete time steps.
- the agent takes action at at state $s_t$ at time $t$, by following certain policies or rules, which will result in a new state $s_{t+1}$ as well as a reward $r_t$.

- If we consider infinite horizon problems with a discounted cumulative reward objective $R_t = sum_{t'=1}^{\infty}\gamma^{t' - t}r_{t'}$ ($\gamma \in [0, 1]$ is the discount factor), the **aim** for the agent is to find optimal policy $\pi : s \to a$ by maximizing its expected discounted cumulative rewards.

## Q-learning
> is a value-based method for solving RL problems by encoding policies through the use of **action-value functions**:
![[Pasted image 20230409134102.png]]

* Optimal value function $Q^*(s, a) := max_{\pi}(s, a)$
* optimal policy $\pi^* \in \operatorname{argmax}_aQ^*(s, a)$

Drawback - typically, Q-value function relies on **all possible state-action pairs**, which are **often impractical to obtain**. 

One **solution** for addressing this challenge is to approximate Q(s, a) using a **parameterized function**


## Deep Q-Network (DQN)
* **Some of approximations is DQN (Deep Q-Network)** - it approximates the Q-value function with a non-linear deep convolutional network, which also automatically creates useful features to represent the internal states of the RL, as shown in [[Pasted image 20230409134439.png]]
* In DQN, the agent interacts with the environment in $i$ discrete iterations, aiming to maximize its long term reward
* DQN has shown great power in generating sequential objects by taking a series of actions. A sequential object is **generated based on a sequence of actions that are taken**:
	* During the generation, DQN selects the action at each step using an $\epsilon$-greedy implementation.
	* With probability $\epsilon$, a random action is selected from the range of possible actions, otherwise the action which results in high Q-value score is selected.
	* To perform experience replay, the agent’s experiences $e_t = (s_t, a_t, r_t, s_{t+1})$ at each time-step $t$ are stored in a data set $D_t = \{e_1,\dots , e_t\}$
	* At each iteration $i$ in the learning process, the updates of the learning weights are applied on samples of experience $(s_t, a_t, r_t, s_{t+1}) \sim U (D)$, drawn randomly from the pool of stored samples, with the following loss function:
		![[Pasted image 20230409135250.png]]
		where $θ_i$ -   parameters of the Q-network at iteration $i$ 
		and $θ^{-}_i$ -  parameters used to compute the target at iteration $i$.
	* The target network parameters $θ^{-}_i$ are only updated with the Q-network parameters $θ_i$ **every several steps** and are **held fixed between individual updates**. 
	* The process of generating the data after training is similar to that of the training process



## Workflow of DQN
![[Pasted image 20230409134439.png]]