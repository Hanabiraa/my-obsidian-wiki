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






## Workflow of DQN
![[Pasted image 20230409134439.png]]