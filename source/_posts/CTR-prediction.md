---
title: CTR prediction
date: 2023-02-09 19:33:09
tags: [推荐系统, 机器学习, 神经网络, 人工智能]
categories: [索道集]
ccover: https://pica.zhimg.com/v2-9cf5d7450dc336709ed1e5d82320b173_1440w.jpg?source=172ae18b
typora-root-url: CTR-prediction
---

# CTR Prediction

Click-through rate (CTR) prediction点击率预测的目标是预测用户点击给定商品的概率。如何提高CTR预测的准确性仍然是一个具有挑战性的研究问题。与其他数据类型(如图像和文本)相比，CTR预测问题中的数据通常采用表格格式，由多个不同字段的数值、类别或多值(或序列)特征组成。**样本量通常很大，但特征空间高度稀疏。**例如，Google Play中的应用推荐涉及数十亿个样本和数百万个特征。

#### CTR预测模型的组成

一般来说，CTR预测模型由以下几个关键部分组成:

1. Feature Embedding: 
