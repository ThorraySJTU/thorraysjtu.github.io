---
layout: post
title: 论文阅读-5：基于边界细化和全局上下文的深度网络显著性检测
category: Paper
tags: [Paper, Saliency Detection]
---

# [论文阅读-5] Saliency Detection by Deep Network with Boundary Refinement and Global Context

## 会议：ICME-2018

## 简介：

本文提出了一种新的端到端的全卷积神经网络，旨在于细化边界和囊括全部信息（... aiming at refining the boundary and covering the global context ...）。为了解决原有卷积神经网络边缘模糊、显著性物体不清的问题，本文提出将边缘强化模块嵌入到网络当中来细化边缘（... embed the boundary enhancement block (BEB) into the network to refine edge ...），通过互耦卷积层来保留细节信息（... keeps the details by the mutual-coupling convolutional layers ...）。此外，本文使用金字塔池化在多层特征信息中找全局内容，同时也起到一个辅助监督的作用。

## 已有的方法

- MDF：使用多尺寸的深度特征来训练显著性模型
- MTDS：使用数据驱动的策略来对潜在的显著性先验知识进行编码（... it takes a data-driven strategy for encoding the underlying saliency prior information ...）
- DCL：提出了一个利用两个互补成分的深度网络（像素级别的全卷积流+分割级别的空间池化流）（pixel-level fully convolutional stream+segment-wise spatial pooling stream）

## 网络结构

![](http://thorraysjtu.github.io/img/20200205/structure.png)

网络结构包括一个用ImageNet预训练的VGG-16。为了优化边界，在网络中添加了三个边缘强化模块，利用金字塔池化结构将conv3-3，conv4-3和conv5-3的特征结合并获取整体内容信息。尽管特征图的分辨率在变化，全局平均池化层（Global average pooling）可以把特征图转变为1\*1的分辨率，然后在进行融合。将融合后的结果通过上采样至64\*64分辨率，再与conv5-3作和。本文一共有5个损失函数，均采用交叉熵的形式进行计算。

### 边缘强化模块（BEB）

![](http://thorraysjtu.github.io/img/20200205/BEB.png)

- 灵感来源于**GCNet**
- 边缘强化模块的输入是conv2-2，conv3-3和conv4-3的输出特征，它的输出保留了与输入相同的分辨率，同时尽管网络深度增加，它可以保留原始网络的特征（... maintains the characteristics of the original network in spite of the depth increasing ...）

#### 结构细节

一个分支的输入输出是直接相连的，另外两个分支都是由空洞卷积（dilated convolution kernel）构成的，空洞卷积之后是一个卷积核尺寸为1\*1大小的卷积操作，左右分支的区别是卷积核膨胀的尺寸。

#### 相互耦合（Intercoupling kernel）

右边空洞卷积的膨胀尺寸是左边空洞卷积的膨胀尺寸的两倍，大的卷积核膨胀尺寸可以获得更大的感受野和保持分辨率，但是会忽略一些中间部分的信息。左边较小的空洞卷积可以覆盖中间区域来强化获取信息（... 2-dilated kernel, with its respective field equaling to 49, to cover the central region again to emphasize the most important central region as an intercoupling kernel ...）。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200205/SOTA.png)

## 
