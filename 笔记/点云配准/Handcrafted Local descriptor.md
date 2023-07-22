先粗读（进度在催）了，有时间再对每篇文章进行精读，更新博客

# without LRF

## SpinImage 1997 - TPAMI

**For object**

> How to get a perfect Local descriptor
> 1. 高效：既要有效，也要计算速度快
> 2. 轻量：descriptor 的存储空间要轻量

use a polygonal surface mesh (多边形表面网格)，the vertices (顶点) → 3D points, the edges → adjcency among points 

Two suface similar when the images from many points on the surface are similar

### Method

1. 生成一个 2 D 的坐标步进统计器（SpinImage 描述符）
2. 计算每个 SpinImage 的支持集中点坐标在 Image 上的投影坐标 $(\rho, \phi)$ (计算原理：根据表面网格顶点的三维坐标和法线向量构建一个圆柱体坐标系，但研究坐标系的径向距离 $\rho$, 高度 $z$ -> $\phi$)，如下图
3. 研究投影坐标落在 SpinImage 描述符的像素索引，统计每个像素索引的落点数作为像素属性。（根据灰度原理，越暗的区域越投影点越多）。

![image.png|500](https://cdn.jsdelivr.net/gh/name555difficult/MyCDN/img/PCR_SI202307221525051.png)


对于每个表面网格顶点都应用上述过程，则每个表面网格顶点都会对应一个 SpinImage 描述符用于表面匹配，匹配思路如下：

1. 对于均匀采样的两个表面，对应顶点的 SpinImage 描述符是线性相关的，可采用线性相关系数作为是否匹配的度量。
2. 比较方法：normalize SpinImage 描述符，之后计算 SpinImage 描述符之间的 $L_2$ 距离。

整体匹配过程为：

1. 先根据 SpinImage 匹配思想，找到对于单个顶点，可对应的另一表面顶点的点集（基于几何一致性思想）
2. 与点集中每个点构成点对预测两个表面的刚性变换，从而对齐表面
3. 筛选最高重叠率的刚性变换作为最佳变换输出。

### 优化

##### SpinImage 描述符压缩

使用 PCA 分析进一步对 SpinImage 描述符提特征，从而降维