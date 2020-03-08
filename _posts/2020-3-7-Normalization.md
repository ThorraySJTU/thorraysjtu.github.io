---
layout: post
title: 深度学习1：常用Normalization方法总结
category: Deep Learning
tags: [Deep Learning, Normalization]
---

# 常用Normalization方法总结

常用的Normalization方法主要有，Batch Normalization，Layer Normalization，Instance Normalization和Group Normalization。将输入的feature map shape记为\[N, C, H, W]，其中N表示batch size，即N个样本；C表示通道数；H、W分别表示特征图的高度、宽度。

## 几个方法的主要的区别：

1. BN是在batch上，对N、H、W做归一化，而保留通道 C 的维度。BN对较小的batch size效果不好。BN适用于固定深度的前向神经网络，如CNN，不适用于RNN

2. LN在通道方向上，对C、H、W归一化，主要对RNN效果明显

3. IN在图像像素上，对H、W做归一化，用在风格化迁移

4. GN将channel分组，然后再做归一化

![img](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYz8lmO7YQC1FQ8yFKw5ibW1u7GVpTxm6Kv989mCcRGrQn4jArecib8qOqQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 形象化的解释说明：

如果把特征图比成一摞书，共计N本，每本C页，每页H行，每行W个字：

1. BN 求均值时，相当于把这些书按页码一一对应地加起来（例如第1本书第36页，第2本书第36页......），再除以每个页码下的字符总数：N×H×W，因此可以把 BN 看成求“平均书”的操作（注意这个“平均书”每页只有一个字），求标准差时也是同理。

2. LN 求均值时，相当于把每一本书的所有字加起来，再除以这本书的字符总数：C×H×W，即求整本书的“平均字”，求标准差时也是同理。

3. IN 求均值时，相当于把一页书中所有字加起来，再除以该页的总字数：H×W，即求每页书的“平均字”，求标准差时也是同理。

4. GN 相当于把一本 C 页的书平均分成 G 份，每份成为有 C/G 页的小册子，求每个小册子的“平均字”和字的“标准差”。

## 四种Normalization的介绍：

### Batch Normalization

#### 为什么要进行BN？

1. 在深度神经网络训练的过程中，通常以输入网络的每一个mini-batch进行训练，这样每个batch具有不同的分布，使模型训练起来特别困难。

2. Internal Covariate Shift (ICS) 问题：在训练的过程中，激活函数会改变各层数据的分布，随着网络的加深，这种改变（差异）会越来越大，使模型训练起来特别困难，收敛速度很慢，会出现梯度消失的问题。

**BN的主要思想**：针对每个神经元，**使数据在进入激活函数之前，沿着通道计算每个batch的均值、方差，“强迫”数据保持均值为0，方差为1的正态分布**，避免发生梯度消失。具体来说，就是把第1个样本的第1个通道，加上第2个样本第1个通道 ...... 加上第 N 个样本第1个通道，求平均，得到通道 1 的均值（注意是除以 N×H×W 而不是单纯除以 N，最后得到的是一个代表这个 batch 第1个通道平均值的数字，而不是一个 H×W 的矩阵）。求通道 1 的方差也是同理。对所有通道都施加一遍这个操作，就得到了所有通道的均值和方差。

**BN的使用位置**：全连接层或卷积操作之后，激活函数之前。

**BN算法过程**：沿着通道计算每个batch的均值 / 沿着通道计算每个batch的方差 / 做归一化 / 加入缩放和平移变量a和b（**加入缩放和平移变量的原因是：保证每一次数据经过归一化后还保留原有学习来的特征，同时又能完成归一化操作，加速训练**）

![img](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYzl1TZPerYPSQ0oWUCNHchicia7o0dhY5ByOFpGjH8RiaC9LuflFdpKJiaNQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**BN的作用：**

（1）允许较大的学习率；

（2）减弱对初始化的强依赖性

（3）保持隐藏层中数值的均值、方差不变，让数值更稳定，为后面网络提供坚实的基础；

（4）有轻微的正则化作用（相当于给隐藏层加入噪声，类似Dropout）

**BN存在的问题：**

（1）每次是在一个batch上计算均值、方差，如果**batch size太小**，则计算的均值、方差不足以代表整个数据分布。

（2）**batch size太大**：会超过内存容量；需要跑更多的epoch，导致总训练时间变长；会直接固定梯度下降的方向，导致很难更新。

### Layer Normalization

针对BN不适用于深度不固定的网络（sequence长度不一致，如RNN），**LN对深度网络的某一层的所有神经元的输入**按以下公式进行normalization操作。

![img](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYzU6tXgyzicLaEkmMKxEZBibyc7VTmBU4NrOc8RraPVnqYnRWuvUCCodSQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



LN中同层神经元的输入拥有相同的均值和方差，不同的输入样本有不同的均值和方差。

对于特征图 ，LN 对每个样本的 C、H、W 维度上的数据求均值和标准差，保留 N 维度。其均值和标准差公式为：

![img](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYzdamztrS73ewZAcGRiaVsGPIrQSetglLaibeESxTJpR7UViaqvVlzawib3Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Layer Normalization (LN) 的一个优势是**不需要批训练，在单条数据内部就能归一化**。LN不依赖于batch size和输入sequence的长度，因此可以用于batch size为1和RNN中。LN用于RNN效果比较明显，但是在CNN上，效果不如BN。

### Instance Normalization

IN针对图像像素做normalization，最初用于图像的风格化迁移。在图像风格化中，生成结果主要依赖于某个图像实例，feature map 的各个 channel 的均值和方差会影响到最终生成图像的风格。所以对整个batch归一化不适合图像风格化中，因而对H、W做归一化。可以加速模型收敛，并且保持每个图像实例之间的独立。

对于x来说，IN 对每个样本的 H、W 维度的数据求均值和标准差，保留 N 、C 维度，也就是说，它只在 channel 内部求均值和标准差，其公式如下：

![img](https://mmbiz.qpic.cn/sz_mmbiz_png/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYzpmcfLsgic8EhJZTf1mBWpo7eKxqFwnZGQgk0zicuw3z9Ko7LdVdCcsWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### Group Normalization

**GN是为了解决BN对较小的mini-batch size效果差的问题。**GN适用于占用显存比较大的任务，例如图像分割。对这类任务，可能 batch size 只能是个位数，再大显存就不够用了。而当 batch size 是个位数时，BN 的表现很差，因为没办法通过几个样本的数据量，来近似总体的均值和标准差。GN 也是独立于 batch 的，它是 LN 和 IN 的折中。

**GN的主要思想：**在 channel 方向 group，然后每个 group 内做 Norm，计算 (C/G)\*H\*W 的均值和方差，这样就与batch size无关，不受其约束。

**具体方法：**GN 计算均值和标准差时，把每一个样本 feature map 的 channel 分成 G 组，每组将有 C/G 个 channel，然后将这些 channel 中的元素求均值和标准差。各组 channel 用其对应的归一化参数独立地归一化。

![img](https://mmbiz.qpic.cn/sz_mmbiz_jpg/gYUsOT36vfoMdJzNnwHQ7Elg9fUeYPYzrLbwIETQeQmZDEPTupkrEpXlreVBIibFzcx2OR01LB7Ql8YLvoxZRvQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)