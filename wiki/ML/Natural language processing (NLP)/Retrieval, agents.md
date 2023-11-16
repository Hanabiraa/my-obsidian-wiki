---
created:
  - " 16-11-2023 18:12"
aliases: 
  - retrieval-augmented generation (RAG)
tags:
  - article/
---

# Retrieval, agents

## Recap: [[LLM]]
> very large transformer

- good at - solving your problems :)
- not good at:
	- retaining long context
	- giving exact answers when necessary
	- using up-to-date knowledge


## Retrieval-Augmented generation
- e.g. want to condition our model on a large documentation database
- it's possible to do so with retrieval methods
- basically, connect standard methods like nearest neighbours


### Token-level retrieval

![[Pasted image 20231116182602.png]]


![[Pasted image 20231116183039.png]]

![[Pasted image 20231116183056.png]]

encode database

### Sequence-level retrieval
![[Pasted image 20231116183950.png]]
- train jointly with encoder

![[Pasted image 20231116184936.png]]

![[Pasted image 20231116185224.png]]


## Agents

### Web-GPT
![[Pasted image 20231116190614.png]]

### Toolformer
- teach models to use APIs

## Practical usage

### Langchain

![[Pasted image 20231116193225.png]]

### Transformer Agents
![[Pasted image 20231116193350.png]]

