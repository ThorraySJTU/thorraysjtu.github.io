---
layout: post
title: 论文阅读-11：F3Net：用于显著性目标检测融合，反馈，聚焦
category: Paper
tags: [Paper, Instance segmentation]
---

# [论文阅读-11] F3Net : Fusion, Feedback, Focus for Salient Object Detection

## 会议：AAAI-2020

## 摘要：

大多数现存得显著性物体检测方法都是通过聚合多层特征图来得到更好的效果的（Most of existing salient object detection models have achieved great progress by aggregating multi-level features extracted from convolutional neural network.），由于不同卷积层得感受野大小不同，不同层之间生成得特征不同，常见得特征融合方法忽略了这种不同，可能会导致次优化结果（Common feature fusion strategies ignore these differences and may cause suboptimal solutions）。本文提出了F3Net用以解决上述提出得问题，其主要是由CFM（交叉特征模块）、CFD（级联反馈解码器）和PPA（像素位置感知损失）组成。

## 简介：

现存的两大显著性目标检测的挑战：

![](http://thorraysjtu.github.io/img/20200304/1.png)

- 不同层次的特征具有不同的特征分布（Feature of different levels have different distribution characteristics）

  - 深层特征具有丰富的语义，但是缺少精确的位置信息（rich semantics but lack accurate location infomation）
  - 浅层特征具有丰富的细节，但是充满了背景噪音（rich details but full of background noises）

  没有合理的控制输入模型的信息流，冗余信息会对最终结果有所影响（Without delicate control of the information flow in the model, some redundant features will pass in and possibly result in performance degradation.）

- 现存的模型使用BCE，它们对于每一个像素是做相同的处理。但是从直觉上来说（Intuitively），每一个像素应该是有不同的贡献的（... pixel at the boundary are more discriminative and should be attached with more importance ...）。
  - 目前有提出多种边缘损失函数，但很多靠近边界的像素也是容易预测错的（lots of pixels near the boundaries prone to wrong predictions）

## 贡献：

- 本文提出了用来融合不同层级特征的交叉特征模块，它可以提取特征之间的共享部分，消除不同特征之间的背景噪音，同时可以弥补不同特征之间的缺失部分（extract the shared parts between features and suppress each other's background noises and complement each other's missing parts.）
- 本文提出了层联反馈解码器，它可以同时将高分辨率和富有多语义信息的特征反馈以此来修正和优化预测的显著性目标结果（feedback features of both high resolutions and high semantics to previous ones to correct and refine them for better saliency maps generation.）
- 本文设计了像素位置感知损失函数，用来给不同位置分配不同的权重，它可以更好的挖掘包含在特征中的结构信息，同时也可以让网络更关注细节区域（It can better mine the structure infomation contained in the features and help the network focus more on detail regions）

## 网络结构

![](http://thorraysjtu.github.io/img/20200304/4.png)

### 交叉特征模块

本文提出的交叉特征模块可以优化深层特征以及浅层特征，由于感受野的限制（due to the restriction of the receptive field），浅层特征保留了细节但是也包含了背景噪音；深层特征由于多次下采样使他的边缘很粗糙，虽然丢失了很多细节，但是还是具有语义信息和清晰的背景。

CFM通过元素级别的乘法提取深层特征和浅层特征的公用部分，然后再将它们与原始的深层和浅层特征融合。相比较于直接将深层和浅层特征相加，CFM可以让特征吸收有用的信息。

- follow-up processing：对深层和浅层特征分别3\*3大小的卷积层作卷积操作，经过处理的特征会做乘法操作进行融合，最后在加到原始特征上去（卷积操作包括：Convolution、Batchnorm和ReLU）。

![](http://thorraysjtu.github.io/img/20200304/2.png)

### 层联反馈解码器

CFD是基于CFM构造的，用来优化多层特征和逐步生成显著性图。传统的方法是直接聚合多层特征生成最终的特征图，但事实上，不同层级的特征会有冗余或者缺失的信息。CFD包括了多个解码器，每个解码器包括bottom-up过程和top-down过程。

- bottom-up process：特征被从深层到浅层逐渐聚合，聚合的结果是被监督的
- top-down process：由上一流程聚合的要素将直接进行降采样，并添加到CFM导出的先前多级要素中以对其进行优化，优化结果会被传递到下一个解码器中。

![](http://thorraysjtu.github.io/img/20200304/3.png)

### 像素位置感知损失函数

#### BCE存在的三个问题

- 计算每一个像素的损失是独立的，忽略了整体的结构
- 背景是主体，前景的损失函数会被稀释掉（loss of foreground will be diluted）
- BCE处理所有像素都是等价的

本文提出了wBCE，来优化这样的问题，局部的结构信息会被编码进wBCE损失函数中，可以帮助模型关注更大的感受野而不是单独的某一个像素。

![](http://thorraysjtu.github.io/img/20200304/5.png)

![](http://thorraysjtu.github.io/img/20200304/6.png)

同时为了让网络可以关注整体结构，文中也提出了wIoU损失函数，其目的也是为了赋予不同位置像素不同的权重。

![](http://thorraysjtu.github.io/img/20200304/7.png)

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200304/8.png)

![](http://thorraysjtu.github.io/img/20200304/9.png)
