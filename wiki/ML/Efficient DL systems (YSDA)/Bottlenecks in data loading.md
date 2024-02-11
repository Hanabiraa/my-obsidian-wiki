---
created:
  - " 11-02-2024 12:56"
aliases: 
tags:
  - article/
---

# Bottlenecks in data loading

## Intro and latency numbers

![[Pasted image 20240211125636.png]]
![[Pasted image 20240211125700.png]]

- sometimes the models aren't so compute intensive
- we still want to process the data efficiently
- need to be mindful of hardware/network performance and the CPU code
- Two components - **what to read** and **how to read**
	- Obvious part: read data in parallel (several processes, asynchronously with computation) - **don't copy to the same space which is used by the model $\implies$ data corruption**

## Storage formats
- raw files are often easy to visualize, but storage-inefficient (especially when accessing external storage)
- In some cases, might benefit from better formats:
	- structured data - Apache Arrow/Protobuf/msgpack etc.
	- for images, apply non-random "heavy" processing before training
	- for language data, tokenize the texts and store indices only

## Minimizing preprocessing time
- Reading the data and feeding it into the model can also be slow
	- For large images, can be bounded by CPU operations
	- For sequence data, can waste time on padding tokens

### Performance of image loading
- Consider the code that reads them
	- PIL.Image.OPen can be inefficient, use at least Pillow-SIMD
	- use better decoders (jpegturbi, nvJPEG from DALI)
- Heavy groups of augmentaions can also slow down
	- Consider moving them on GPU (e.g. kornia)
	- In most cases, can switch to efficient implementations

### Optimal sequence processing
- padding batches is necessary
- ENTIRE dataset - leads to redundant timesteps
- It's usually better to store samples without padding and use `collate_fn`
- Also bucket examples by length to further minimize padding
> ![[Pasted image 20240211130817.png]]

## Takeaways
- consider performance/size of a storage when loading the data
- Use better deserialization primitives when available
- Try to avoid obvious inefficiencies when building task-specific pipelines