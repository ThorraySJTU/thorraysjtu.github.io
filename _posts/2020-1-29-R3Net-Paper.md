---
layout: post
title: 论文阅读-1：用于显著性目标检测的循环残差优化网络
category: Paper
tags: [Paper]
---

# [论文阅读-1] R3Net : Recurrent Residual Refinement Network for Saliency Detection

## 会议：IJCAI-2018

## 简介：

本文提出了一种循环残差精化网络用来进行显著性目标检测（Recurrent Residual Refinement Network）。文中设计了残差精化模块（RRB）用来将深层特征与先前预测的显著性结果结合，并当作输入用来学习真值和先前预测的显著性结果的残差。(... first design a residual refinement block, which takes the deep features concatenated with the previous predicted saliency map as inputs to learn the difference (residual) between the ground truth and the previous saliency map ...) 。此外，文中在FCN中嵌入一系列的残差精化模块用来构建一个循环残差精化网络。它可以通过选择性地利用深层分割特征以及浅层细节特征作为输入来逐步地优化显著性图。（... embed a sequence of RRBs in an FCN to construct our recurrent residual refinement network, which progressively refines the saliency maps at each recurrent step by alternatively leveraging the high-level semantic features and the low-level detailed features as the inputs ...）

## 存在的问题

- 传统的方法：利用人为设计的特征（hand-crafed visual features）或者启发式优先级（heuristic priors），**不可以捕捉深层的分割信息**（... incapable of capturing the high-level semantic knowledge ...）

  深度显著性网络：利用全卷积网络的深度分割特征，**忽视了很多细化的显著性细节**（... neglect many fine saliency details ...）

- 近期工作：利用短连接（short connection）将全卷积网络的多层特征进行融合，**只在一个阶段进行预测**（conduct the prediction at one stage）

  通过利用一个阶段级别的精化网络来处理不同阶段的显著性预测（... leverage a stage-wise refinement network to process the saliency prediction in many stages ...），**这种方式趋于说明非显著性区域主要依靠浅层特征来优化显著性预测**（... tends to introduce non-salienct regions as it mainly relies on the low-level features to refine the saliency maps ...），**在每一个阶段用整个网络来精化并不足以学习到显著性目标**（... insufficient to learn salient objects for refinement using a plain network at each stage, **because it produces the refined saliency prediciton from the scratch without preserving previous prediction** ... ）

## 网络结构

![](http://thorraysjtu.github.io/img/20200129/结构图.png)

- 利用特征提取网络来生成一系列的特征图，其中包括浅层的细节信息以及深层的分割信息。
- 浅层和深层分别生成浅层集合特征L（前三个卷积块）以及深层集合特征H（后两个卷积块）。
- 通过深层集合特征生成初始的显著性图，选择性地利用L和H和一系列的残差精化模块来逐步优化显著性图。
- 在每一个循环步，利用监督信号来计算真值和预测的显著性图之间的损失值。

### 残差精化模块

选择性地将L或者H与上一步中预测的显著性图结合当作输入，输出一个加上了学习到残差项的上一步的预测结果的优化显著性图（outputs a refined saliency map by adding the previous saliency map with a learned residual）。

第j步的残差项的计算：将j-1步的显著性图与特征图F结合，然后进行3次卷积操作，每次卷积后PReLU作为激活函数，得到残差项。

第j步的显著性图：将第j步的残差项与第j-1步的显著性图进行像素级别的加法。

### 循环残差精化网络

文中使用ResNeXt作为特征提取的网络来生成一系列不同尺寸的特征图。深层的特征图小尺寸，可以捕捉到显著性物体深层的分割信息；浅层的特征图大尺寸，可以提取惊喜复杂的结构信息。

将前三层进行上采样并融合得到浅层集合特征L；将后两层进行上采样并融合得到深层集合特征H。

本文的网络利用深层集合特征H得到了初始的显著性预测结果，而利用H是为了获得显著性物体的大致位置。由于浅层集合特征可以从输入图片中获得更多的显著性细节，所以将L设定为第一个RRB的特征图集合。同时浅层集合特征也包含一些非显著性信息，所以在第二个RRB模块中使用深层集合特征来关注显著性目标的分割信息。为了改善显著性预测结果，之后的RRB中选择性地合并L和H。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200129/STOA.png)





