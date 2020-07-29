---
layout: post
title: 论文阅读-16：学习深度关系提升显著性目标检测
category: Paper
tags: [Paper, Saliency Detection]
---

# [论文阅读-16] Learning Deep Relations to Promote Saliency Detection

## 会议：AAAI-2020

## 简介：

本文通过挖掘深层特征的普遍关系来有效的提升显著性检测（This paper explores the ubiquitous relations on the deep features to promote the existing saliency detectiors efficiently.）。文中通过最大化互信息来建立彼此关系，根据阈值限制训练对简历策略来保证可以准确地简历不同图片部分的的关系-通过自监督的形式（a threshold-constrained training pair construction strategy to ensure that we can accurately estimate the relations between different image parts in a selfsupervised way）。

## 存在的问题

- 模型在一些不确定的显著性区域预测效果不好（unsure about some confusing saliency area)
- 特征空间独立性（在训练的过程中，邻域特征向量没有信息的传递）

## MI for Deep Relation Estimation

![](http://thorraysjtu.github.io/img/20200729/Structure.png)

- Mutual Information in Saliency Detection

  - 互信息的运用可以测量两个随机变量的相关性（模糊的区域如果和确定的前景区域有很大的互信息，那么就会被判定为显著性区域）

- Derivation

  - 用卷积特征提取器来提取输入输入图片的鲁棒性强和有表述性的特征向量
  - X ~ 确定的前景区域的特征向量；Y ~ 随机区域的特征向量

  ![](http://thorraysjtu.github.io/img/20200729/Dev.png)

  ![](http://thorraysjtu.github.io/img/20200729/JS.png)

- Optimization

  - 二项交叉熵损失函数

### Mutual Information Relation Model

### Multi-scale Feature Extraction

基学习器：MobileNet v2，通过基学习器来获取不同深度的特征。把网络的第0层、第3层、第13层和第17层收集起来，上采样（双线性插值）到同样大小，通过ASPP模块将高分辨率特征图的通道数减少到32。

训练样本：

**Guide mask**：

通过基学习器生成一个先验的显著性图。通过先验显著性图可以引导建立训练样本对，并且可以给出建议标签。

三个mask的生成：FG-mask / AFG-mask / ABG-mask

**Construct training pairs** ：

 将所有得doubtless前景特征与一个FG堆叠在一起，将准前景区域和后景区域分别生成positive pair和negative pair，将训练对输入discriminator中，并训练整个网络。

![](http://thorraysjtu.github.io/img/20200729/pair.png)

### Saliency Map Generating

![](http://thorraysjtu.github.io/img/20200729/Finally.png)

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200729/SOTA.png)
