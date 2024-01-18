---
created:
  - " 18-01-2024 18:05"
aliases: 
tags:
  - article/
---

# Lecture 1 - introduction

## Bird's eye view of DL

1) Training
	1) How to achieve the best quality?
	2) Do I utilize resources to the fullest?
	3) How to navigate hundreds of experiments?
	4) How to avoid bugs in pipeline?
2) Inference - usage of a model
	1) Is performance good enough for my use case?
	2) How do I ensure the model is maintainable?
	3) How to avoid bugs in pipeline?

## [[GPU architecture]]

## Measuring performance
- benchmarking - understand bottlenecks and measure the impact of optimization
- `%%timeit` / `timeit.Timer` - **mind synchronization** 
- Take into account some warmup and caching and other side-effects methods
- `torch.utils.benchmark` - can use it
- **Do not overoptimize!!!**

