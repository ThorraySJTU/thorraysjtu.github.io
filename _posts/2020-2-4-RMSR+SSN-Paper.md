---
layout: post
title: 论文阅读-4：基于最小显著区域回归的螺旋共享网络显著性检测
category: Paper
tags: [Paper, Saliency Detection]
---

# [论文阅读-4] Learning the Spiral Sharing Network with Minimum Salient Region Regression for Saliency Detection

## 会议：ICASSP-2018

## 简介：

本文提出了一种两阶段的显著性目标检测方法。第一阶段是用来回归最小包括显著性物体的显著性区域（... to regress the minimum salient region containing all salient objects ...），第二阶段，为了融合多层次特征，本文提出了螺旋分享网络用来对上一阶段的结果作像素级别检测（... the spiral sharing network is proposed for pixel-level detection on the result of RMSR ...）。

## 存在的问题

![](http://thorraysjtu.github.io/img/20200204/weakness.png)

- 对于相同类别的物体很难区分他们的显著性（to tell the saliency with objects in the same category in the image）
- 背景和显著性物体的颜色分布相近，很难区分出显著性物体（... the background is similar to the salient objects in color distribution, it is not easy to distinguish salient objects ...）

## 网络结构

![](http://thorraysjtu.github.io/img/20200204/structure.png)

### 最小显著性区域回归（RMSR）

- 显著性区域用两个坐标来描述：包含显著性物体的长方形区域的左上角和右下角坐标
- VGG-16用来回归显著性区域，真值是通过已有的显著性数据集得到的bounding box
- 损失函数是euclidean损失，通过RMSR，可以将显著性物体从复杂背景分割出来

### 螺旋分享网络（SSN）

- SSN的输入是根据RMSR的输出得到的剪裁结果
- 螺旋结构包括四个数据旁路块（skip-layer block）。通过这种方法可以在不同的分支和层级之间分享多个特征（share multiple features from different branches and layers）
- 分支a从conv1-2通过数据旁路将信息传递到分支b，同理分支b传递到分支c，分支c传递到分支a，分支a传递到分支b，通过四个数据旁路块形成一个螺旋结构
- 用交叉熵来作损失函数
- 数据旁路块的输出是特征图经过3*3卷积层和ReLU激活函数和输入的特征图的和，以此保留低层信息。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200204/SOTA.png)

将前三层进行上采样并融合得到浅层集合特征L；将后两层进行上采样并融合得到深层集合特征H。

本文的网络利用深层集合特征H得到了初始的显著性预测结果，而利用H是为了获得显著性物体的大致位置。由于浅层集合特征可以从输入图片中获得更多的显著性细节，所以将L设定为第一个RRB的特征图集合。同时浅层集合特征也包含一些非显著性信息，所以在第二个RRB模块中使用深层集合特征来关注显著性目标的分割信息。为了改善显著性预测结果，之后的RRB中选择性地合并L和H。

## 与SOTA结果比较
