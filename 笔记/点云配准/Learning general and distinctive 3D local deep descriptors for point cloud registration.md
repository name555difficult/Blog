Author: Fabio Poiesi and Davide Boscaini

#2022_  #TPARMI 

[Code](https://github.com/fabiopoiesi/gedi)

# Abstract

We present a simple yet effective method to learn **general and distinctive 3D local descriptors** that can be used to register point clouds that are captured in different domains

***High Generalization***

Paper Work:

- **Get LRF** (Local Reference Frame): achieving rotation invariant by computing a LRF
- **Point patches -> canonical representation**: rigidly transform point patch to its canonical representation using LRF. (seems like regularization)
- **Quaternion (四元组) transform network (QNet):** solve the problem of a noisily estimated LRF by refining the canonicalisation operation
- **Point permutation-invariant deep network**: **canonical representation -> the local descriptor**.Our deep network **uses receptive fields with different radii** to aggregate local geometric information **at multiple scales**

We train QNet and the encoding network concurrently through a **Siamese approach** by using **contrastive learning**

# PipLine

# Related work

## Handcrafted 

Typically, handcrafted methods encode local geometric structures as **histograms** of, e.g., pointcoordinates, surface normals, and/or pairwise point/normal relationships

### With LRF

1. `MCOV Descriptor`: 

### Without LRF

## Deep Learning-based

deep learning-based methods learn **dense or sparse representations** of local geometric structures. 

### With LRF

### Without LRF