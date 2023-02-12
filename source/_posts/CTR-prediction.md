---
title: CTR prediction
date: 2023-02-09 19:33:09
tags: [推荐系统, 机器学习, 神经网络, 人工智能]
categories: [索道集]
cover: https://github.com/PaddlePaddle/PaddleRec/raw/master/doc/imgs/rec-overview.png
typora-root-url: CTR-prediction
---

# CTR Prediction

> 本文参考:
>
> [BarsCTR:]: https://arxiv.org/abs/2009.05794	"《BarsCTR: Open Benchmarking for Click-Through Rate Prediction》"
>
> 

Click-through rate (CTR) prediction点击率预测的目标是预测用户点击给定商品的概率。如何提高CTR预测的准确性仍然是一个具有挑战性的研究问题。与其他数据类型(如图像和文本)相比，CTR预测问题中的数据通常采用表格格式，由多个不同字段的数值、类别或多值(或序列)特征组成。**样本量通常很大，但特征空间高度稀疏。**例如，Google Play中的应用推荐涉及数十亿个样本和数百万个特征。

### CTR预测模型的组成

一般来说，CTR预测模型由以下几个关键部分组成:

1. Feature Embedding: CTR预测的输入实例通常包含三组特征，即，用户简介，项目简介，以及上下文信息. 每组特征都有一些字段，如下所示:

   - 用户简介:年龄、性别、城市、职业、兴趣等
   - 商品简介:商品ID、类别、标签、品牌、卖家、价格等。
   - 上下文:工作日、时间、位置、槽位号等。

   每个字段中的特征可以是类别、数值或多值的(例如，单个项目的多个标记)。由于大多数特征非常稀疏，经过one-hot或multi-hot编码后形成高维特征空间，通常采用特征嵌入将这些特征映射到低维密集向量中。下面我们总结了类别，数值，多值这三种特征的嵌入过程。

   - 类别：对于类别`i`,给定one-hot特征向量$x_i$, 嵌入矩阵$V_i \in R^{d\times n}$, `d`是嵌入维度, `n`是字母表个数(类别个数). 嵌入后的向量是`d * 1`的. 
   - 数值: 对于数值特征`j`, 我们有多种嵌入方式:
     - 通过手动设计(例如，将13 ~ 19岁的年龄分组为青少年)或通过在数字特征上训练决策树(例如，GBDT)，然后将它们以类别特征的方式嵌入;
     - 给定一个规范化的标量值$x_j$, $e_j = v_j x_j, \quad v_j \in R^d$是所有特征`j`共享的嵌入向量; 
     -  除了将每个值存储到一个类别中或为每个数值字段分配一个向量, 还可以利用`AutoDis`, 一种数值特征嵌入方法，利用元嵌入矩阵对数值特征进行动态类别化和嵌入计算。
   - 多值: 对于多值特征`h`, 每个特征都可以表示为一个序列. $e_h = V_h[x_{h_1},x_{h_2},x_{h_3},...,x_{h_k}] \in R^{d \times k}$, 其中$x_{h_k}$是一个one-hot向量, `k`表示序列的最大长度, 嵌入结果$e_h$可以被进一步嵌入为一个`d`维向量(均值池化/求和池化). 进一步的潜在改进是应用序列模型，如DIN中的目标注意力和DIEN中的GRU，来聚合多值行为序列特征。

2. Feature Interaction: 在特征嵌入后，可以直接应用任何分类模型进行CTR预测。然而，对于CTR预测任务，特征之间的相互作用(又称特征连接, feature conjunctions)是提高分类性能的核心。在因子分解机(factorization machines, FM)中指明，内积为捕获成对特征相互作用的简单而有效的方法。自从FM的成功以来，大量的研究都致力于以不同的方式捕捉特征之间的相互作用。自从FM的成功以来，大量的研究都致力于以不同的方式捕捉特征之间的相互作用。此外，目前大多数工作研究一种将显式和隐式特征交互与普通全连接网络(即mlp)结合起来的方法。

3. Loss Function: 二元交叉熵损失在CTR预测任务中被广泛应用，其定义如下;
   $$
   L = -\frac{1}{N}\Sigma_D (y\ log\hat y + (1-y)log(1-\hat y))
   $$
   其中`D`是有`N`个样本的数据集, `y`和$\hat y$分别表示真实的和估计的点击概率, $\hat y = \sigma (\phi (x)),\quad \phi(x)$代表模型函数, CTR预测建模的核心在于如何构建模型$\phi(x)$, 并通过训练数据学习模型参数. 

### 经典模型

本节详见参考论文

1. Shallow Models: 工业CTR预测任务通常具有大规模的数据。因此，浅层模型因其简单高效而得到了广泛的应用。即使在今天，LR[40]和FM[39]仍然是工业中部署的两个强大的基线模型。(e.g. LR, FM, FFM, HOFM, FwFM, LorentzFM)
2. Deep Models: 目前，深度神经网络在CTR预测方面得到了广泛的研究和应用。与浅模型相比，深度模型在利用非线性激活函数捕捉复杂的高阶特征交互方面更强大，通常会产生更好的性能。然而，在实际应用中，效率已成为深度模型规模化的主要瓶颈。(e.g. DNN, CCPM, Wide&Deep, IPNN, DeepCross, NFM, AFM, DeepFM, DCN, xDeepFM, HFM+, FGCNN, AutoInt+, FiGNN, ONN, FiBiNET, AFN+, InterHAt)
