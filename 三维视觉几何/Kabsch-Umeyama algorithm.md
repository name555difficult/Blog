# Kabsch-Umeyama algorithm

参考文献：

1. [https://www.wikiwand.com/en/Kabsch_algorithm](https://www.wikiwand.com/en/Kabsch_algorithm)

面向点云配准，最小化两点集**均方根误差(RMSD, root mean squared deviation)**来计算最佳旋转矩阵。
> 注：该算法只能计算旋转矩阵，做点云配准还需要计算平移向量。当平移和旋转都正确计算出，该算法有时也叫_[partial Procrustes superimposition](https://www.wikiwand.com/en/Procrustes_superimposition) (see also [orthogonal Procrustes problem](https://www.wikiwand.com/en/Orthogonal_Procrustes_problem))_

步骤：

1. a translation
2. the computation of a covariance matrix
3. the computation of the optimal rotation matrix

## First Step: Translation

将两组点集的质心对齐坐标系原点：
$$
\hat{P} = || P - P_{centroid} ||_1
$$

## Second Step: calculate the covariance matrix

calculate the corrs-covariance matrix $H$ between $P$ and $Q$
$$
H = P^TQ
$$

## Third Step: calculate the optimal rotation matrix

base calculating formula:
$$
R=(H^TH)^{\frac{1}{2}}H^{-1}
$$
但是如果出现一些特殊情况，例如：$H$ 不存在逆矩阵时，上述公式变得复杂，甚至不可解。所以一般都会使用SVD（奇异值分解，singular value decomposition）变换上述公式。
$$
SVD: H=U\Sigma V^T
$$
并且校正旋转矩阵确保在一个右手坐标系，最终计算最佳旋转矩阵$R$ 。
$$
d = sign(det(VU^T))
$$

$$
R = V \begin{pmatrix} 1&0&0\\0&1&0\\0&0&d\end{pmatrix}U^T
$$

> 最佳旋转矩阵还可以用四元数（[quaternions](https://www.wikiwand.com/en/Quaternion)来表示）

## Generalizations

The algorithm was described for points in a three-dimensional space. The generalization to *D* dimensions is immediate.

