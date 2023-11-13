---
created:
  - " 09-11-2023 18:09"
aliases:[Conversational systems]
tags:
  - article/
---

# Chat bots

## History

### ELIZA
![[Pasted image 20231109181052.png]]
- Repeats your question through a question
- Manually programmed
- *ELIZA effect* - people who use the chatbot report that ELIZA feels like *she* is almost human
- There were others - PARRY (1971) - emulates schizophrenia speech

### CleverBot

- Has a database of human responses
- picks the closest one by context (e.g. KNNCLassifier)
- Database contains conversations with users. Notoriously toxic.

### Siri, Alisa, Oleg, Google Home
![[Pasted image 20231109181644.png]]


## Goal-oriented chat bots
and voice assistants

### System design - split "chat bot" into smaller problems
- Natural language understanding (NLU)
- JSONify information about the answer
- answer to an user

![[Pasted image 20231109182643.png]]


#### **Natural Language understanding**


**Named Entity recognition** 
	- extract keywords from user's message, use them, e.g.
![[Pasted image 20231109182924.png]]![[Pasted image 20231109183001.png]]
**Semantic parsing**
	- figure out what user wants from you
- **Anaphora resolution**
	- find what "it" or "this bar" refers to
	- restore information which is not given in the last sentence
- **Ellipsis**
	- recover any missing from information from context

#### Dialogue Management (DM)
1) Dialogue state tracking - what is the state of the dialogue?
![[Pasted image 20231109184333.png]]
2) Dialogue strategy
- how to respond?
- Do we nned any extra info?

choose one at random OR with reinforcement learning
![[Pasted image 20231109184726.png]]


Or - prompt language model

#### Natural language generation



## LLM-based chatbots

### Recap: T0 pre-training
- generates answers for various tasks
![[Pasted image 20231109185051.png]]

### Want - instruction following
![[Pasted image 20231109185226.png]]

### Instruction Tuning
![[Pasted image 20231109185421.png]]
#### InstructGPT
![[Pasted image 20231109185741.png]]

##### **Step 1. Supervised fine-tuning (SFT). Collect demonstration data and train a supervised policy.**
- prompt is sampled from prompt dataset
- labeler demonstrates the knowledge
- data is used to fine-tuned GPT-3 with supervised learning


- Cheaper version: train LoRA adapters

**Why can't we stop at SFT stage?** (*not enough*)
1) ranking is easier than writing for labelers
	1) easier to collect less SFT data and the n use RL to maximize preferable answers
2) SFT promotes hallucinations

##### **Step 2. Reward model.**

train reward model
![[Pasted image 20231109194046.png]]


![[Pasted image 20231109194149.png]]

![[Pasted image 20231109194525.png]]
- reward model should output one number

$r_{\theta}$ - reward model. $y_w$ - reward winning, $y_l$- reward for losing hypothesis



##### Step 3. Reinforcement learning

![[Pasted image 20231109195932.png]]

$\pi(a|s)$ - policy of an action given some state. $\pi(a|s)=\prod_{y_i\in a}P(y_i|s)$.


**REINFORCE algorithm**
Average reward:

$$\mathcal{J} = \sum_a \pi_{theta}(a|s) * R(a,s)$$

$R(a, s)$ - reward. This expression is too costly to calculate. Can approximate:

$$\frac{1}{N}\sum_{a_i}^NR(a_i,s)$$
- can calculate it, but cannot maximize


Gradient for loss (with fixed $\theta$, reward doesn't depend on $\theta$), estimation:
$$\nabla_{\theta}\mathcal{J} = \frac{1}{N}\sum_{a_i}^NR(a_i,s)\nabla\log\pi(a_i|s_i)$$

Some draft from desk:
![[Pasted image 20231109201334.png]]

Do gradient ASCENT on this.


**Proximal Policy optimization (PPO) algorithm (family of algorithms)**:
![[Pasted image 20231109202131.png]]
- clip reward -> agent doesn't do some radical things and doesn't forget something rare




