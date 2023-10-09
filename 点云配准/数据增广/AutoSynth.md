# AutoSynth: Learning to Generate 3D Training Data for Object Point Cloud Registration

**2023 ICCV**

> ***Zheng Dang, Mathieu Salzmann\***; Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV), 2023, pp. 9009-9019

- paper: [ICCV 2023 Open Access Repository (thecvf.com)](https://openaccess.thecvf.com/content/ICCV2023/html/Dang_AutoSynth_Learning_to_Generate_3D_Training_Data_for_Object_Point_ICCV_2023_paper.html)
- code: 

**总结** ：本文提出了一个自动化方法 —— AutoSynth，生成面向物体点云配准的训练数据。该方法将生成训练数据定义再包含数百万个不同潜在3D形状的潜在数据集上，以一个real-object dataset为引导来搜索出一个最佳数据集，使得对于目标task在搜索出的最佳数据集上训练后，在real-object dataset上的测试性能最佳。为了减轻训练成本，加快搜索速度，这里的目标task不采用通常的配准模型，为了加快速度，采用了encoder-decoder架构进行reconstruction任务，实验表明这样的方法与采用配准模型进行搜索的性能基本一致，甚至有所超越。并且搜索出来的最佳数据集用在训练配准模型，然后在其他real dataset上测试，性能与SOTA差距不大。

![image-20231009155749141](https://img2023.cnblogs.com/blog/3251700/202310/3251700-20231009160211762-1945063607.png)

<!--个人评价：重建任务能与配准任务一致，表明了在物体配准这一任务上，语义感知能力更为重要。其使用Autoencoder提供强有力的语义感知方式表明了2D vision到3D vision的壁垒越来越薄了-->

## Abstract

文章立意：n this work, we address this by introducing an approach dubbed AutoSynth, automating the process of curating a 3D dataset. Specifically, we aim for the resulting dataset to act as effective training data for a 3D object registration network that will then be deployed on real-world point clouds.

> 提出了一个自动化方法生成面向物体点云配准的训练数据 —— AutoSynth，该方法低成本地探索包含数百万个不同3D形状的潜在数据集的搜索空间，从而自动规划一个最佳数据集。The search **is guided by a target realworld dataset**, thus producing data that reduces the domain gap

keword：assembing shape primitives(组装形状基元)、meta-learning strategy(元学习策略)、evolutionary algorithm

>  To make the search tractable, we observe that the true quality function, i.e., the accuracy of the registration network of interest, can be replaced with a proxy one, i.e., the reconstruction accuracy of an autoencoder. 所以又用了MAE了吗
>
> - registration accuracy and reconstruction quality follow the same trend

## Contribution

Our main contributions can be summarized as follows:

- We present AutoSynth, a novel meta-learning-based approach to automatically generate large amounts of 3D training data and curate an optimal dataset for point cloud registration. 
- We show that the search can be made tractable by leveraging a surrogate network that is 4056.43 times more efficient than the point cloud registration one.  这个没理解，使用autoencoder代替吗，然后怎么做呢
- We evidence that using a single scanned real-object as target dataset during the search yields a training set that leads to good generalization ability.

## Description

**问题描述** ：自动生成一个仿真数据集，使得主要的任务模型，比如说点云配准模型，能够在生成的仿真数据集上训练到收敛以后，在测试集上精度最高。由于测试数据集在训练时不适用，因此我们用target dataset（real-world）作为测试数据集。

![image-20231008205408401](https://img2023.cnblogs.com/blog/3251700/202310/3251700-20231009160212325-1358141469.png)

