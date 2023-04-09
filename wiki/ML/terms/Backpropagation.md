---
created: [" 07-04-2023 18:51"]
aliases: [бэкпроп, backpropagation]
tags:
- terms/
---

# Backpropagation

## definition

**algorithm for training feedforward artificial neural networks or other parameterized networks with differentiable nodes**


- Based on autodifferentiation algorithm


1) Each layer of neural network is some differentiable function
2) Overall Neural network workflow is composition of functions
3) We can train neural networks by tuning its parameters
4) Parameters are tuned proportionally to its contribution to the error, which is comouted through chain rule
5) Tuning values are propagated backwards in layers order - from finish to start - that's why it is called backpropagation

## Useful links
It is used in almost EVERY ML/DL algorithm :)