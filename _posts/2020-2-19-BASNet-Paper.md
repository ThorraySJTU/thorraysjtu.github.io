---
layout: post
title: 论文阅读-9：BASNet:关注边缘的显著性物体检测
category: Paper
tags: [Paper, Saliency Detection]

---

# [论文阅读-9] BASNet：Boundary-Aware Salient Object Detection

## 会议：CVPR-2019

## 摘要：

本文提出了一种预测优化框架，BASNet，以及一个混合损失函数用来作关注边缘的显著性物体检测（... propose a predict-refine architecture, BASNet, and a new hybrid loss for Boundary-aware salient object detection ...）。模型框架是由一个密集监督编码-解码网络和残差优化模块组成的。损失函数从层次像素级别、补丁级别和图级别使网络学习输入图片和真值之间的转化（The hybrid loss guides the network to learn the transformation between the input image and the ground truth in a three-level hierarchy-pixel-, patch- and map-level- by fusing BCE, SSIM and IoU losses ...）。

## 存在的问题

- **全卷积网络的预测结果在细节结构和边界上还有瑕疵**（... their predicted saliency maps are still defective in fine structures and/or boundaries ...）。
- 显著性大多是依靠整张图的全局对比度来定义的，而**忽略了局部或者像素级别的特征**（... the saliency is mainly defined over the global contrast of the whole image rather than local or pixel0wise features ...），为了解决这种问题，网络需要聚集多层深度特征（To address this problem, networks that aggregate multi-level deep features are needed ...）。
- 大多显著性物体检测是使用交叉熵作为训练损失函数，但是交叉熵损失函数**很难区分边界像素**，**会导致边界模糊问题**（... trained with CE loss usually have low confidence in differentiating boundary pixels, leading to blurry boundaries ...）。

## 贡献

- 本文提出了一个新的关注边缘的显著性物体检测网络，其包括了一个深度监督编码-解码模块和残差优化模块
- 本文提出了一个新的混合损失函数用来融合BCE，SSIM和IoU这三种损失函数来监督训练过程

## 网络结构

![](http://thorraysjtu.github.io/img/20200219/structure.png)

### 对于网络结构的分析

BASNet的预测模块是一个类U-Net的密集监督编码-解码网络，用来从输入图像中学习预测的显著性图，多尺寸的残差优化模块通过学习显著性图和真值之间的残差来优化预测模块得到的显著性图（... refines the resulting saliency map of the prediction module by learning the residuals between the saliency map and the ground truth）。

### 预测模块

**为了减少过拟合的情况，在每个解码阶段的最后一层都会通过真值图进行监督式的学习**，编码层有一个输入卷积层和六个有res块组成的阶段（The encoder part has an input convolution layer and six stages comprised of basic res-blocks），输入卷积层和前四个阶段来自于RerNet-34。**区别**在于本文的输入层有64个卷积滤波器（大小为3\*3，步长为1\*1），在输入层后没有池化层。**在第二阶段前的特征图和输入图有着相同的分辨率**（... the feature maps before the second stage have the same spatial resolution as the input image），这种调整让网络可以在浅层就获得高分辨率的特征图，同时减少整体的感受野（This adaptation enables the notwork to obtain higher resolution feature maps in earlier layers, while it also decreases the overall receptive fields ...），在第四阶段后，作者又添加了两个阶段用来获得与ResNet-34相同的感受野（由三个基本的res块组成，每个块中有512个卷积滤波器）。

为了捕获整体的信息，文中在编码和解码中间加入了一个桥连阶段（a bridge stage between the encoder and the decoder），有三个卷积层组成（512个大小为3\*3空洞卷积滤波器 + BN + ReLU）。解码部分大致是编码部分的对称结构（卷积层后连接着BN和ReLU激活函数）。

### 优化模块

![](http://thorraysjtu.github.io/img/20200219/RRM.png)

为了同时优化区域和边缘上的缺陷，文中提出的RRM是一种残差编码-解码结构，与预测模块类似，有输入层、编码块、桥连层、解码层和输出层组成。编码层和解码层都有四个阶段，每个阶段只有一个卷积层（包含64个大小为3\*3的卷积滤波器 + BN + ReLU），桥连层和每个阶段中的结构类似。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200219/SOTA.png)
