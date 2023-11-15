---
created:
  - " 15-11-2023 18:06"
aliases: 
tags:
  - article/
---

# Video analysis

## Video

> sequence of frames obtained from a single camera in short periods of time

- **Videostram** assumes online processing of frames, may be unbounded in time
- **Videosequence** assumes offline processing, all frames are available

> Need to compress video to reduce traffic usage

- to reduce processing time, apply some large model to some interval of frames


## Optical flow
> vector field of visible movements of pixels between frames


![[Pasted image 20231115181742.png]]

- can predict using neural network

### **What about data?** - hard to create by human

#### Middlebury dataset
- get some flourescent color
![[Pasted image 20231115182049.png]]


#### KITTI dataset
- 3D reconstruction of environemnt
- apply laser scanning for estimating and creating initial training data
![[Pasted image 20231115182159.png]]

#### 3D render

E.g., **Sintel** - 3d render movie

#### Neural networks

**FlowNet**
Concatenate a pair of images and pass it to prediction network:
![[Pasted image 20231115183043.png]]

- has some limitations

![[Pasted image 20231115183248.png]]

- Synthetic data is very useful for this task!

**PWC-Net**

**RAFT**

**FlowFormer** - SOTA


## Visual object tracking

> tracking of an object localized on the first frame
> online mode
> similar and occluded objects make task more comple
![[Pasted image 20231115190241.png]]


