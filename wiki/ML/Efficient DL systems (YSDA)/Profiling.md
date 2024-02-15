---
created:
  - " 11-02-2024 13:13"
aliases: 
tags:
  - article/
---

# Profiling

## What and why
- In benchmarking, we measure the spped of a program as a black box
- Profiling is a process of determining the runtime of **parts** of a program
- More of a "white box" approach

## How to profile
> ![[Pasted image 20240211131456.png]]

## How to profile GPU code?
- nvprof - low-level profilng tool
> ![[Pasted image 20240211131655.png]]

### PyTorch code
> ![[Pasted image 20240211131800.png]]
Integration with tensorboard
> ![[Pasted image 20240211132003.png]]

## Nsight Systems/Nsight Compute - full-fledged instruments
> ![[Pasted image 20240211132147.png]]

### Typical patterns:
> ![[Pasted image 20240211132534.png]]

## Main task - make GPU always busy