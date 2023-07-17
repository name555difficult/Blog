For handling severe geometric variations e.g. object scale, viewpoints (rotation in 3D) and part deformations.

Solve: **Modeling spatial transformation** 

# First Paper --- 2D vision

[Cylindrical Convolutional Networks for Joint Object Detection and Viewpoint Estimation](https://arxiv.org/abs/2003.11303)

![image.png|500](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/CCN_202307160025069.png)


## Simple Introducion

**Convolutional kernel is exploit cylindrical representation**， CCNs **extract a view-specifific feature through a view-specifific convolutional kernel** to predict
object category scores at each viewpoint. With the view specifific feature, we simultaneously determine objective category and viewpoints using the **proposed sinusoidal soft-argmax module**.

**Problem statement**: Given a single image of objects, our objective is to jointly estimate object category and viewpoint to model viewpoint variation of each object in the 2D space
   
### PipLine

![image.png|1000](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/CNN_202307161550160.png)

**Dataflow**: 

- inputing Single viewpoint image of a object to full convolutional network get *input feature map* ($R^{k*k*ch_i}$ );
- Set $N_v$ view-specific kernel ($R^{k * k * ch_i * ch_o}$ ) for processing input feature map;
- Get output feature map ($R^{ch_o*N_v}$) by cylindrical convolution kernel.

**Formulation**:

$$
F_v = \sum_{p\in R}W_v(p)·x(p) = \sum_{p \in R}W_{cyl.}(p+o_v)·x(p)
$$

- $W_v$ : a view-specific cylindrical convolutional kernel $R^{k * k * ch_i * ch_o}$;
- $W_{cyl}$: weigth parameter of $W_v$;
- $o_v$: an offset on cylindrical kernel;
- $p$: the position p varies within in the k * k window R;
- x: input feature map; 
- $F_v$: a output feature vector ($R^{ch_o}$) in out feature map.