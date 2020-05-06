---
layout: post
title: 论文阅读-15：深度诱发多尺寸循环注意力网络
category: Paper
tags: [Paper, attention]
---

# [论文阅读-15] Depth-induced Multi-scale Recurrent Attention Network for Saliency Detection

## 会议：ICCV-2019

## 简介：

文中提出了一个深度诱发多尺寸循环注意力网络（Depth-induced Multi-scale Recurrent Attention Network）。

**存在的问题**：

![](http://thorraysjtu.github.io/img/20200505/1.png)

- 大多数基于CNN的方法融合RGB和深度特征是直接在深层或浅层通过concatenation或者summation实现的。
- 多物体在同一个场景中的深度和尺寸并不一致。

## 贡献

- 本文设计了一个高效深度精化模块（DRB），利用 残差连接在多层提取和融合RGB和深度特征。
- 本文设计了一个深度诱导多尺寸权重模块。
- 本文设计了一个循环注意力模块，可以从粗到细更加准确的生成显著性检测目标。

## 方法

![](http://thorraysjtu.github.io/img/20200505/2.png)

网络结构是一个two-stream的模型，基本架构是VGG-19的五个卷积块。深度流的结果是生成一个深度向量，用来和RGB流的结果结合输入到DRB模块中。在深度向量的引导下，多尺寸特征会经过融合特征在DMSW模块中生成。在模型的结束，会通过循环注意力机制来提升模型的效果。

**深度精化模块**

![](http://thorraysjtu.github.io/img/20200505/3.png)

每一层都会有一个RGB特征一个深度特征，深度特征会经过卷积和PReLu来学习一个深度残差。和RGB特征进行加操作后，reshape到指定大小，再通过一个残差学习，得到每一个卷积块的最终特征。

**深度诱导多尺寸权重模块**

深度特征的最后一层经过均值池化和卷积操作学习深度向量，深度精化模块学习到的结果融合后得到，用不同尺寸的卷积核提取信息（Inception思想）。

**循环注意力机制**

![](http://thorraysjtu.github.io/img/20200505/5.png)

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200505/4.png)
