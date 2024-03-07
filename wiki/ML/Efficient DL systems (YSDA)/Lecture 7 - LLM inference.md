---
created:
  - " 07-03-2024 18:04"
aliases: 
tags:
  - article/
---

# Lecture 7 - LLM inference

## Особенности инференса
Надо работать:
- Быстро
- Стабильно
- Надежно


### Stages and latencies
1) TTFT - **Time To First Token** (First token latency, first chunk latency)
2) Generation step - **Tokens Per Second**

Latency trade-off
![[Pasted image 20240307181607.png]]

## Stages of a pipeline
![[Pasted image 20240307182006.png]]

LLM response pipeline
![[Pasted image 20240307182212.png]]

**Decoder** **model** - decides which token to send to output

