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

# Formulation statement of Method

$$
\bf{f} = (\Phi_{\Theta}\circ\Psi)(\mathcal{X})
$$
- $\mathcal{X} = \{\bf{x}\} \subset P$ , unorder set of points patch
- $\Psi$ : the function that samples and canonicalises $\mathcal{X}$ through LRF
- $\Phi_{\Theta}$: a deep network with learnable parameters $\Theta$ for computing local discriptior
- $\bf{f}$: $R^d$ is the d dimensional descriptor of $\mathcal{X}$

# PipLine

![image.png](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/202307171659367.png)

## Local sampling and canonicalisation

Preprocess before network compution: Patch extraction -> LRF estimation -> sampling of the points within the patch

### 1. Patch extraction

$$
\mathcal{X} = \{x:||x- \hat{x}||_2 \leq r\}
$$
-  $\hat{x} \in P$ is the patch centre, $r$ is the patch redius 

$$
\widetilde{\mathcal{X}} = S_m(\mathcal{X}),\ \ \widetilde{\mathcal{X}} \subset \mathcal{X}\ \&\&\  |\widetilde{\mathcal{X}}| = m
$$
- $\widetilde{\mathcal{X}}$ is this step output, $m$ is the number of predefined patch points, if $|\mathcal{X}| \lt m$ , padding to $m$ points by sampling with replacement from $\mathcal{X}$
- Same point cardinality also enable us to effificiently train $\Phi_\Theta$ using minibatches.

### 2. LRF estimation

1. Using TOLDI Method to estimate LRF and compute $R_{\hat{x}}$ (rotation matrix for transforming  $\widetilde{\mathcal{X}}$ from the reference frame of $P$ to the LRF of $\mathcal{X}$) 

### 3. sampling of the points within the patch

1. Random sample $n$ ($n \lt m$) points to get point set $\overline{\mathcal{X}}$ 

$$
\overline{\mathcal{X}} = S_n(\widetilde{\mathcal{X}}),\ \ \overline{\mathcal{X}} \subset \widetilde{\mathcal{X}}\ \&\&\  |\overline{\mathcal{X}}| = n
$$
2. normalise the coordiantes of  $\overline{\mathcal{X}}$: 

$$
\Psi(\overline{\mathcal{X}}) = \{y: y = R_{\hat{x}}(\frac{x-\hat{x}}{r}), x\in \overline{\mathcal{X}}\}
$$

## Deep network design



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
	3. `Point-Pair Features(PPF)`: is designed as **point representations in the form of a hash table** in order to make the task of point cloud registration effificient. PPF is more suitable for object 6D pose estimation.

## Deep Learning-based

deep learning-based methods learn **dense or sparse representations** of local geometric structures. 

### With LRF

Firstly preprocess point patches by using LRF, Secondly input to deep network to compute descriptors

- 3DSmoothNet: using TOLDI’s LRF, and transforms points into volumetric voxel grids and processes them with a 3D convolutional network like *The 3DMatch approach* , but insted of TDF value, 3DMoothNet **computes Gaussian smoothed representations** based on the coordiantes of the points within the patch.
- the distinctive local 3D descriptors (DIP): also use TOLDI to canonicalise points and PointNet to produce Local descriptors.DIP uses **a transformation network on the input points** to improve eventual noisy canonicalisations.
- The local multi-view descriptors (LMVD): be computed through **multi-viewpoint image rendering** through a differentiable renderer.For a point of interest, LMVD **defines the LRF of the viewpoint by using the point normal and a consistent upright orientation**. LMVD produces descriptors by extracting local features maps from each viewpoint and by **aggregating them via soft-view pooling**
- [Paper](https://arxiv.org/abs/1909.06887):  propose an **unsupervised method** to learn descriptors by using a **Spherical CNN encoder** that produces rotation-equivariant representations and a decoder that reconstructs the input points. **Authors show how the LRF can be learnt in a end-to-end manner by exploiting the Spherical CNN output as it lives in SO(3).**


### Without LRF

model descriptor rotation invariance by either considering pairs of points, or by learning it via data augmentation, or by simply ignoring it.

- The 3DMatch approach: transforms patches into **volumetric voxel grids (体积度量的体素网格) of Truncated Distance Function (TDF) values (截断距离函数值)** and processes them through a **3D convolutional network** to output local descriptors
- PPFNet: #supervised adds normal and PPF representation of the patch to the point coordinates, and processes it with a PointNet-based network. Get local and global features to build context-informed descriptors.
- PPF-FoldNet: #Unsupervised  **with means of an autoencoder**, try to reconstruct the input PPF representations, without the point coordiante and normal information. Produce local descriptor from the PPF representation. Based on PointNet like PPFNet.
- FCGF: using a **full convolutional network** to produce local descriptor trough 3D sparse convolution. FCGF processes the whole point cloud in one pass and outputs a **descriptor per point**.
- D3Feat: extracts descriptors by using dense deep features through a **KPConv backbone**.
- SpinNet: using a spatial point transformer to get rotation invariant representation of point patch, then project the input points to a cylindrical space, finally use a **3D cylindrical convolutional layers** is then utilised to output **patch-based** descriptors.