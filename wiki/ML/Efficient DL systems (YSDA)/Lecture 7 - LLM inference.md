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


### KV cache
**Decoder** **model** - decides which token to send to output

Decoder block:
![[Pasted image 20240307182442.png]]

Generation steps:
![[Pasted image 20240307182747.png]]
- get tokens one by one and then use newly generated tokens $\to$ quadratic complexity

**KV-cache** - get token after token
![[Pasted image 20240307182517.png]]
- Only the last token computes attentions to all tokens (without computing masks)
- Generation is **linear** in memory 


## Batches
For some layers - use padded version and for others - use unpadded

![[Pasted image 20240307183107.png]]
![[Pasted image 20240307183116.png]]

### Continuous batch
- generation of requests 
![[Pasted image 20240307183453.png]]

## Flash attention
![[Pasted image 20240307183733.png]]

SRAM - static random access memory

latencies for different memory usages:
![[Pasted image 20240307183757.png]]

Tiling:
![[Pasted image 20240307183918.png]]

## Flash attention 2
- new CUTLASS library release


## Flash decoding

Block reduce in splits:
![[Pasted image 20240307184821.png]]

There is some loss in accuracy


## Long sequence batches

### Paged attention
![[Pasted image 20240307185400.png]]

Batching in tokens, not in queries



## Frameworks

![[Pasted image 20240307190340.png]]

