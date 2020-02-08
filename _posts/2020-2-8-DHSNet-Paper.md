---
layout: post
title: 论文阅读-6：DHSNet:深度层次显著性目标检测网络
category: Paper
tags: [Paper, Saliency Detection]
---

# [论文阅读-6] DHSNet:Deep Hierarchical Saliency Network for Salient Object Detections

## 会议：CVPR-2016

## 简介：

本文提出了一种新的端到端深度层次显著性目标检测网络（DHSNet）。DHSNet首先通过学习不同的整体结构显著性线索做一个粗略的整体预测（... makes a coarse global prediction by automatically learning various global structured saliency cues ...），然后利用层次循环卷积神经网络（HRCHH）来集成局部信息逐渐优化细节（... is adopted to further hierarchically and progressively refine the details of saliency map step by step via integrating local context information ...）。整体的DHSNet是一个从粗到细从整体到局部的过程。

## 存在的问题

- 传统的方法：利用对比度，这种方法**很难保留物体的细节**（... fail to preserve object details ...），**不容易检测到大尺寸、复杂纹路、与背景颜色相近的物体**（... are difficult to detect salient objects with large sizes and complex texture, especially when image backgrounds are also cluttered or have similar appearances with foreground objects）
- 近期的工作：利用多种先验知识（utilize various prior knowledge as informative saliency cues），边缘先验**显著性物体接触图片边缘时或者与背景相近**（... fials when salient objects touch image boundaries or have similar appearance with background），密实度先验（compactness prior）和物体先验（objectness prior）它们通常是通过经验进行探索，并通过手工设计的公式进行建模（... explored empirically and modelled by hand-designed formulations ...）。
- 在部分工作中，会选择添加一些用来预测显著性基本的计算单元和一些用来修饰显著性图的后处理方法（... the basic computational units to predict saliency or the post-processing methods to smooth saliency maps ...），这样会降低计算效率，增加时间花费（... are usually very time-consuming, becoming the bottleneck of the computational efficiency of a salient object detection algorithm.）

## 贡献

- 提出了一种新的端到端的显著性目标检测网络，可以同时学习特征表征，显著性细节信息，将二者结合的最优机制以及进一步优化显著性图的细节。（simultaneously learn powerful feature representation, informative saliency cues and their optimal combination mechanisms from the global view, and subsequently learn to further refine saliency map details）
- 提出了一种新的层次优化模型，用来集成局部信息并不断优化显著性图

## 网络结构

![](http://thorraysjtu.github.io/img/20200208/structure.png)

- DHSNet是由GV-CNN和HRCNN组成的
- GV-CNN是用来从一个整体的视角来对显著性物体进行一个粗略的检测，HRCNN逐渐的来优化细节。

### 用来粗略预测的GV-CNN

由VGG网络的13个卷积层和一个全连接层以及一个reshape层组成，在Conv5_3之后连接一个由sigmoid作为激活函数的全连接层生成784个结点，随后reshape成28\*28大小的粗全局显著性图（coarse global saliency map）

### 用来层次优化显著性图的HRCNN

#### 循环卷积层（RCL）

![](http://thorraysjtu.github.io/img/20200208/RCL.png)

RCL将循环连接融入到每一个卷积层中（... incorporates recurrent connections into each convolutional layer ...）,将T-1层的显著性图与卷积层的特征结合置于RCL中，通过这个前馈子网得到T层的显著性图并传入T+1层中（A RCL with T time steps can be unfolded to a feed-forward subnetwork of depth T+1）。在RCL单元中，随着时间步的增长，有效感受野在不断扩张（... the effective receptive field of an RCL unit expands when the time step increases ...）。

#### 层次显著性图的优化

将每一个时间步的显著性图进行上采样，然后与每个卷积块的最后一个卷积层结合，通过RCL生成该时间步的的显著性图。通过GV-CNN得到的显著性图SM0与Conv4_3（28\*28）结合，置于RCL中，得到SM1；SM1上采样，与Conv3_3（56\*56）结合，至于RCL中，得到SM2，以此类推，得到最后的显著性图。

将前三层进行上采样并融合得到浅层集合特征L；将后两层进行上采样并融合得到深层集合特征H。

本文的网络利用深层集合特征H得到了初始的显著性预测结果，而利用H是为了获得显著性物体的大致位置。由于浅层集合特征可以从输入图片中获得更多的显著性细节，所以将L设定为第一个RRB的特征图集合。同时浅层集合特征也包含一些非显著性信息，所以在第二个RRB模块中使用深层集合特征来关注显著性目标的分割信息。为了改善显著性预测结果，之后的RRB中选择性地合并L和H。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200208/SOTA.png)
