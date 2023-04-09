---
created: [" 09-04-2023 13:16"]
aliases: [GRNN, RNN]
tags:
- article/
---

# Generative recursive neural network

## RNN (recurrent neural network) brief review
- RNN is a straightforward adaptation of the standard feed-forward neural network by using their internal state (memory) to process variable length sequential data. 
- At each step, the RNN predicts the output depending on the previous computed hidden states and updates its current hidden state, that it, they have a “memory” that captures information about what has been calculated so far. 
- The RNN’s high dimensional hidden state and nonlinear evolution endow it with great expressive power to integrate information over many iterative steps for accurate predictions. 
- Even if the non-linearity used by each unit is quite simple, iterating it over time leads to very rich dynamics

## Standard RNN formalization
given a sequence of input vectors (x1, ..., xT ), the RNN computes a sequence of hidden states (h1, ..., hT ) and a sequence of outputs (o1, ..., oT ) by iterating the following equations from t = 1 to T :
![[Pasted image 20230409131926.png]]
where U , V , and W are learning weight matrices; the vectors bh and bo are biases for calculating the hidden states and output at each step, respectively. 
- The expression V ht−1 at step t = 1 is initialized by a vector, h0, and the tanh nonlinearity activation function is applied coordinate-wise
## Modification to generative model
- The goal of modeling a sequence is to predict the next element in the sequence given the previous generated elements. 
	- More formally, given a training sequence (x1, ..., xT ), RNN uses the sequence of its output vectors (o1, ..., oT ) to parameterize a sequence of predictive distributions p(xt+1|x≤t). 
- The distribution type of p(xt+1|x≤t) need to be assumed in advance. 

For example, to determine the category of the discrete data $x_{t+1}$, we can assume a softmax distribution as:
![[Pasted image 20230409132159.png]]

where j refers to one of the categories of the object, o(j) t refers to the j-th variable in the output vector ot and K refers to the total number of categories of the objects. 


The objective of **modeling sequential data** is to **maximize** **the total log likelihood of the training sequence**  $\sum_{t=0}^{T-1}log p(x_{t+1}|x_{\le t})$, which implies that the RNN learns a joint probability distribution of sequences. 

Then we can generate a sequence by sampling from p(xt+1|x≤t) stochastically, which is parameterized by the output at each step.


## Workflow
![[Pasted image 20230409131733.png]]
