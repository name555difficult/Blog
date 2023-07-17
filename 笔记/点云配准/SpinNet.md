# Method

## Fisrt Step：Spatial Point Transformer 

transform point cloud to rotation invariant representation without dropping critical information of local patterns.

![image.png|1000](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/SpinNet_202307151707405.png)

1. Rotation invariant representation: 3D cylindrical volume
2. data flow：point cloud -> Spherical Voxel -> Cylindrical volume

### 1. Alignment with a Reference Axis

### 5. Cylindrical Volume Formulation

reformulate the spherical voxels into a cylindrical volume for Feature extractor which is the 3 D cylindrical CNN

The cylindrical volume formulated as $C \in R^{J*K*L*K_v*3}$ ， each spherical voxel is transformed as a cylindrical voxel in cylindrical volume

## Second: Feature  Extractor 

把每一个 patch 的所有 voxel 重整化到一个 cylindrical Volume 里。 

![image.png|1000](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/SpinNet_202307151711640.png)

### 1. Point-based Layers 

- **Structure**: Multi-MLPs -> Max Pooling
- **data flow**: points of one Voxel $R^{k_v*3}$ -> $R^{D}$ feature of one Voxel      czv 

$$
f_{jkl} = A(MLPs(points\ of\ one\ Voxel))
$$
Get feature maps of all cylindrical voxels $F\in R^{J*K*L*D}$  

### 2. 3 D Cyclindrical convolutional Layers

 # Experiment 

-  dataset: 3DMatch (indoor) and KITTI (outdoor)
- 

