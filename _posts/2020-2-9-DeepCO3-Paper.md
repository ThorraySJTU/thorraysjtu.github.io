---
layout: post
title: 论文阅读-7：DeepCO3：基于共峰搜索和共显著性检测的深度实例共分割
category: Paper
tags: [Paper, Instance Co-segmentation]


---

# [论文阅读-7] DeepCO3：Deep Instance Co-segmentation by Co-peak Search and Co-saliency Detection

## 会议：CVPR-2019

## 简介：

本文提出了一种新的CV任务实例共分割（instance co-segmentation），给出一系列包含某特定类别物体的图片，识别其中所有实例并分割。本文为了解决这个问题将任务分成了两个子任务，共峰搜索以及实例掩码分割（co-peak search and instance mask segmentation）。对于前者，文中设计了一个基于CNN的网络用来检测两张图片的共峰和共显著性图（... to detect the co-peaks as well as co-saliency maps for a pair of images ...）。对于后者，本文设计了一个评分函数，以检测到的共峰图和共显著图为输入，选择目标方案产生最终结果（... takes the detected co-peaks and co-saliency maps as inputs and can select the object proposals to produce the final results ...）。

## 存在的问题

- 基于CNN的方法通过端到端的方法可以学习常见物体的表征（learn the representation of common objects in an end-to-end ），同时也可以生成高质量物体级别的结果，但**不可以探查实例感知信息**。
- 物体共分割问题：**类别内部的巨大变化以及背景的杂波**（large intra-class variations and background clutter）。
- 实例感知分割问题：针对class-aware方法，**无法事先知道图中所有的物体种类，限制了class-aware方法的效果**；针对class-agnostic方法，**现存的class-agnostic方法需要对训练数据进行物体边界的标注**（... require annotated training data in the form of object contours ...）。

## 贡献

- 提出了一种新的CV任务，实例共分割。
- 针对实例共分割问题提出了一个简单有效的解决方法。基于全卷积神经网络，通过三种损失函数优化（共峰、仿射和显著性损失），可以检测出用来实例掩码分割的共峰和共显著性图。

## 网络结构

![](http://thorraysjtu.github.io/img/20200209/structure.png)

### 整体结构

给出一系列含有某特定类别的图片集D，图片共分割的目的是识别并分割出每一个在D中的实例。本文将任务分为共峰搜索和实例掩码分割两个部分。在共峰搜索部分，本文旨在两张图片中找到共峰部分，共峰对应两个鉴别点和相似点（... a co-peak corresponds two discriminative and similar points, one in each image ...）。本文对于共峰检测，先通过全卷积网络来对输入图片进行特征提取，将输出结果分为两个流。一个流关联两幅图像的特征映射用来实现共峰定位（One stream correlates the feature maps of two images for co-peak localization），另一个流用来对输入图片进行显著性采样（The other estimates the co-saliency maps of input images）。

### 共峰检测

对于两张图片，每一张图片有w\*h\*d大小，本文的模型在特征提取之后分为两个流，其中一个流将两张图的特征融合成一个4D tensor，大小为w\*h\*w\*h，每一个位置都记录存储在两个空间位置的特征向量之间的标准化内积（... records the normalized inner product between the feature vectors stored at two spatial locations ...）。另外一个流通过1\*1的卷积层进行共显著性的采样。文中提出的三个损失函数，共峰损失用来模拟共峰检测，仿射损失代表共显著图并能启用区分特征学习（... refers to the co-saliency maps and enables discriminative feature learning ...），显著性损失与其他两个损失一起工作可以执行共同显著性检测（... working with the other two losses carries out co-saliency detection ...）。

#### 共峰损失

在本文提出的网络中，其中一个流得到了两张图片的显著性图，另一个流得到了图像间关联图（inter-image correlation map）。为了得到一个显著性引导的tensor，我们将第一张图p位置的显著性值与第二张图q位置的显著性值相乘再乘到关联途中对应的两个坐标位置的地方，最终得到新的关联图。文中定义共峰通过局部最大化这个关联图，通过最终得到的关联图来计算共峰损失。

#### 仿射损失

仿射损失旨在得到显著性区域中的像素彼此相似而与背景中不同的特征。（... aims to derive the features with which pixels in the salient regions are similar to each other while being distince from those in the background ...）。

#### 显著性损失

该项旨在辨别显著性区域并引导模型训练。通过使用SVFSal网络训练的结果来当作“真值”，并计算显著性图与预测结果之间的区别，每一项前有一个权重用来解决显著性区域和非显著性区域之间的不平衡问题。

### 实例掩码分割

通过上一步的操作，我们可以得到每张图中一系列的峰（peak），随后文中利用峰值反向传递的方法得到对于每一个峰值的实例感知的热力图。对于实例掩码的生成，文中使用MCG（multi-scalre combinatorial grouping），文中将热力图和显著性图一起融合到评价函数中用来得到最终的实例掩码分割。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200209/SOTA.png)
