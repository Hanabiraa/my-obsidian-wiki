---
created:
  - " 11-02-2024 11:46"
aliases: 
tags:
  - article/
---

# Mixed precision training

## Floating point numbers

* Neural networks require real numbers, which need to be represented in finite memory
* Single precision (FP32) is the default format with 4 bytes of storage:
	![[Pasted image 20240211115123.png]]


	- overflow => nan or $\pm \inf$
	- $\frac{\inf}{0} \implies$ nan

### Why use low precision?
- faster performance 
- reduced memory usage (due to higher arithmetic intensity or smaller communication footprin)
- can use specialized hardware for even faster computation