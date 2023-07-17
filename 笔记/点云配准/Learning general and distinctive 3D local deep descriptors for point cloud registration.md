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

Firstly **estimating a LRF** of point patch, Secondly **building a compact representation** as local feature descriptor.

- The Instrinsic Shape Signatures (ISS): 
	- `Estimate LRF`: with a **wighted covariance matrix** that aggregate the geometric properties of points within the patch, The weights penalise distant points from the centroid. LRF is obtained through the **eigenvalue decomposition** (特征值分解) of the wighted covariance matrix.
	- `Build a compact representation`: using the **occupational histogram of a spherical neighbourhood** with a predefined radius around the centroid.
- The Signature of Histograms of Orientations (SHOT): 
	- `Estimate LRF`: the eigenvalue decomposition of the covariance matrix to **find two orthogonal axes** and disambiguates the sign of these axes, then The third axis is **the cross product** between these two axes.
	- `Build a compact representation`: a **combination** of an occupational histogram and of the relative geometric properties within the patch
- The Triple Otrhogonal Local Depth Images (TOLDI):
	- `Build a compact representation`: extends SHOT’s LRF by introducing a weighted aggregation of the points.
	- `Estimate LRF`: the 2D histograms of **the projections** which Points are projected on the three 2D planes defined by its LRF.
- Rotation Projection Statistics (RoPS):
	- `Estimate LRF`: using covariance matrix based on **the local connectivity of the points**.
	- `Build a compact representation`: be built using low-order central moments (低阶中心矩) and entropy (熵) of these points .

### Without LRF

从点本身或者表面法向量制作特征描述

1. using 3D point coordinates:
	1.  `MCOV Descriptor`: use a **covariance matrix** encoding the correlation between the geometric and the photometric information of a patch, such as relative angles and RGB values of points.
2. using surface normals: （局部表面法线向量）
	1. `SpinImage`：based on **rotation invariant parametric representations** of points within a patch . These representations are projected on a 2D image plane and mapped to a descriptor by **computing a 2D histogram**（先计算点的旋转不变性表示，之后投影到 2 维平面计算统计直方图）
	2. `Fast Point Feature Histograms (FPFH)`: be computed as distances and relative angles between points and normals, then FPFH is defifined as **the histogram of these features**. FPFH can be used to register large point clouds.
	3. `Point-Pair Histograms(PFH)`: is designed as **point representations in the form of a hash table** in order to make the task of point cloud registration effificient. PPF is more suitable for object 6D pose estimation.

## Deep Learning-based

deep learning-based methods learn **dense or sparse representations** of local geometric structures. 

### With LRF

### Without LRF