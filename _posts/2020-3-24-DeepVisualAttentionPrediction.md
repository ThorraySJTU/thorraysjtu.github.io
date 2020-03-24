---
layout: post
title: 论文阅读-13：深度视觉注意力预测
category: Paper
tags: [Paper, attention]

---

# [论文阅读-13] Deep visual attention prediction

## 会议：TIPS-2018

## 简介：

本文旨在基于端到端的深度学习网络预测人眼关注点（human eye fixation）。文中的方法用来捕捉层次性的显著性结构从深层全局的显著性信息到浅层局部的显著性反馈。文中模型是依靠跃层网络结构（skip-layer network structure）来预测人的注意力机制。本文的做法区别于以往的方法只在输出层提供监督（providing supervision only at the output layer），文中将监督模块注入在多层中（supervision is directly fed into multi-level layers）。

传统模型分为两类：bottom-up方法和top-down方法；

bottom-up方法是刺激性驱动（stimulus-driven），在没有任何图像内容信息地情况下，基于visual stimulus来指明人的注意力关注什么。

top-down方法是任务驱动的（task-driven），需要额外的对于场景内容的理解（require explicit understanding of the context of scene）。

## 贡献

- 本文研究了卷积神经网络显著性预测问题，文中提出的模型可以在一个网络中捕获多层显著性信息。
- 模型采用了深度监督的方式进行训练，将监督直接反馈到多个层次，从而自然地学习到多个层次的显著性信息。
- 模型适用于编码器-解码器架构，解码器使用可训练地多通道内核执行上采样。

## 方法

![](http://thorraysjtu.github.io/img/20200324/Structure.png)

VGGNet网络，第三个卷积块和第四个卷积块做上采样，与第五个卷积块的输出层融合进行监督学习。

## 与SOTA结果比较

![](http://thorraysjtu.github.io/img/20200324/SOTA.png)



