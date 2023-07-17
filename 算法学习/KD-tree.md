参考：

- [KD-Tree 原理详解]([KD-Tree原理详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/112246942))
- [【量化课堂】kd 树算法之详细篇 - JoinQuant量化课堂 - JoinQuant](https://www.joinquant.com/view/community/detail/c2c41c79657cebf8cd871b44ce4f5d97)

kd-tree 简称 k 维树，是一种空间划分的数据结构。常被用于高维空间中的搜索，比如范围搜索和最近邻搜索。kd-tree 是二进制空间划分树的一种特殊情况 。

在激光雷达SLAM中，一般使用的是三维点云。所以，kd-tree的维度是3。

由于三维点云的数目一般都比较大，所以，使用kd-tree来进行检索，可以减少很多的时间消耗，可以确保点云的关联点寻找和配准处于实时的状态。

