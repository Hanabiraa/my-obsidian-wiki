---
created:
  - " 01-11-2023 18:04"
aliases: 
tags:
  - article/
---

# Object detection

## Task statement, dataset & metrics


### One-class object detection
- Find all objects of a **fixed** class in an image
- Output a set of bounding boxes: $\{(x_y, y_i, h_i, w_i)\}_{i=1}^N$ (bboxes)
- Instead of bboxes:
	- rotated bboxes
	- ellipses
	- pixel mask
### Multiclass object detection
- Find all objects of a **fixed** set of classes in an image
- Output: $\{(x_y, y_i, h_i, w_i, c_i)\}_{i=1}^N$ (additional point-class)
- ! Aim to find **things** (people, cars), not **stuff** (sky, road)

#### Datasets
- MS COCO 
- LVIS

Find datasets for domain!

### Metrics
* [[IoU matching criterion|= IoU]]
* Computing precision & recall ([[Precision & Recall]])
	* Match predicted bboxes with ground truth bboxes using predicted confidence to compute TP, FP, FN
	* ![[Pasted image 20231101182200.png]]
	* unmatched positives - FP
	* unmatched GT - FN
* Miss-rate vs. FPPI

### Annotation protocol
- can be different


## Object detection via classification


**Basic options:**
- base - sliding window
![[Pasted image 20231101183828.png]]
- multiscale sliding window or Multiresolutional pyramid - for detectiob of the object with variable size
- multiple aspect ratios - if objects have variable size ration
**What to do with multiple windows with detections?** -Non-maximum suppression (NMS)
![[Pasted image 20231101184235.png]]
- can get average or cluster windows

### **Problem - imbalanced classes**
- too many patches with noise (not faces)
- need to determine which patches to include in the training
#### Hard negative mining

![[Pasted image 20231101184510.png]]

- expensive => do few iterations (2-5)


## Neural nets

### R-CNN
![[Pasted image 20231101184803.png]]
- region proposals - hypotheses
- 3 step - get warped regions through AlexNet


### Fast R-CNN
![[Pasted image 20231101184946.png]]
- R-CNN works with potentiall intersected patches independently - redundant computations
- instead, use some pooling (Roi pooling)

#### Spatial Pyramid pooling
![[Pasted image 20231101185223.png]]

#### Roi pooling
![[Pasted image 20231101185307.png]]
![[Pasted image 20231101185325.png]]

#### Roi align
![[Pasted image 20231101185342.png]]
apply bilinear interpolation to better proposals

![[Pasted image 20231101185422.png]]

![[Pasted image 20231101185444.png]]

### Faster R-CNN

get rid of selective search

![[Pasted image 20231101185637.png]]
- conv layers => **Region proposal network**
- bboxes proposals to Roi pooling with feature maps => then to classifier

#### Region proposal network
convolution with sliding window

![[Pasted image 20231101185736.png]]
- regressor of bbox coord
- k-anchor bboxes

## YOLO (You only look once)
"more powerful region proposal network"
1) **Inference** (original methods works with 448x448)
	1) split image into 7x7 boxes
	2) for every cell predict P(object) and bboxes
		![[Pasted image 20231101190210.png]]
	3) For every cell also predict P(class)
	4) Combine bboxes and class probs
	5) Apply NMS and prob theshold
		![[Pasted image 20231101190315.png]]

	![[Pasted image 20231101190330.png]]

YOLO outputs:
![[Pasted image 20231101190423.png]]
2) **Training**
	1) ![[Pasted image 20231101190442.png]]
	2) Look at predicted bboxes
	3) Find nearst bbox and increase P(Object) for bbox:
	![[Pasted image 20231101190519.png]]
	4) Lower bboxes for other cells 

## RetinaNet
- use backbone in detector, which sees on the particular region in an image
- **network receptive field isn't always similar to object size**
![[Pasted image 20231101190813.png]]


#### Feature pyramids!!!


## Anchor-free detection

#### FCOS bbox regression
![[Pasted image 20231101191557.png]]
**Architecture:**
![[Pasted image 20231101191623.png]]

#### DETR
![[Pasted image 20231101191918.png]]

- queries - trainable tokens - query to the net for it to predict an object for given token. Don't contain predefined information

![[Pasted image 20231101192101.png]]
- no object - special prediction
- for others - predict coordinates of bboxes