---
created:
  - " 08-02-2024 18:12"
aliases: 
tags:
  - article/
---

# Distributed ML

## Intro

### Motivation - why?
![[Pasted image 20240208181338.png]]

![[Pasted image 20240208181500.png]]

#### Threads and processes

- processes are split
- threads aren't split


### GloVE example

![[Pasted image 20240208181932.png]]

How to train?
![[Pasted image 20240208182048.png]]


## Formalism

### Process

![[Pasted image 20240208182521.png]]

### Channel/Pipe
![[Pasted image 20240208182702.png]]
- more communication - more costly


![[Pasted image 20240208182937.png]]

*__Asyncio__ use instead of processes when the bottleneck is on your side (user requests)*

## Operation parallelism


### Data parallelism
- each process runs **full** **model** on some **samples** (**partial** data)
![[Pasted image 20240208183202.png]]

- then aggregate gradients

**Optimize**: transfer a portion of parameters

### Model parallelism
- each process runs **partial** **model** on **full data**

![[Pasted image 20240208183714.png]]

**Optimize**
![[Pasted image 20240208183832.png]]

**Operation parallelism nuances**
![[Pasted image 20240208185220.png]]

### Parameter Server
![[Pasted image 20240208190532.png]]

#### Asyncrhonous training
![[Pasted image 20240208190655.png]]

**Have we lost anything by going asynchronous?** Parameters **expire** (**stale gradients**)

### Staleness-aware SGD

![[Pasted image 20240208191148.png]]

![[Pasted image 20240208191913.png]]



