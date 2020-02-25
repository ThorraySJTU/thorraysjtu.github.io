---
layout: post
title: 论文阅读-10：通过ImageNet训练的CNN是纹理偏置；增加形状偏置增加准确率和鲁棒性
category: Paper
tags: [Paper, CNN]

---

# [论文阅读-10] ImageNet-Trained CNNs are biased towards texture; Increasing shape bias improves accuracy and robustness

## 会议：ICLR-2019

## 简介：

![](http://thorraysjtu.github.io/img/20200225/General.png)

卷积神经网络（CNN）通常被认为通过学习对象复杂形状的表示来识别对象（... thought to recognize objects by learning increasingly complex representations of object shapes ...）。最近的一些研究表明图像纹理具有更重要的作用和影响。本文通过CNN模型对具有纹理-形状线索冲突的图像进行分类，以及让人类观察者来识别这些图片对最终的结果进行对比。结果表明，ImageNet训练的CNN偏向于识别纹理而不是形状（...recognizing textures rather than shapes）。在后续实验中，文中基于ResNet-50，利用Stylized-ImageNet，使得模型更倾向于学习基于形状的特征（... is able to learn a shape based representation instead when trained on 'Stylized-ImageNet），这样**改进了对象检测性能，以及对广泛的图像失真具有前所未有的鲁棒性，突出了基于形状表示的优点**。

## 贡献

- 这个实验为纹理假设提供了支持
  - 拥有大象纹理的猫对 CNNs 来说是大象，对人类来说仍然是猫。
- 这个实验提出了改变偏置的方法以及发现了改变偏置的优点
  - 在合适的数据集上训练，可以克服标准 CNNs 中的纹理偏置，使其向形状偏置转变。
  - 具有较高形状偏差的网络对许多不同的图像失真天生就更有鲁棒性 ，并在分类和目标识别任务上达到更高的性能。

## 方法

![](http://thorraysjtu.github.io/img/20200225/accuracy.png)

### 网络模型

AlexNet，GoogLeNet，VGG-16，ResNet-50，将标准的ImageNet在4个CNNs上进行预处理，将1000个ImageNet类预测映射到16个类别之中，飞机、熊、自行车、鸟、船、瓶子、汽车、猫、椅子、时钟、狗、大象、键盘、刀、烤箱和卡车。

### 数据集

原始图片：160张图片自然彩色图片（每类10张）

灰度图：原始数据集中的图像使用skimage.color.rgb2gray转换为灰度

Silhouette：原始数据集中的图像转换为轮廓图像，在白色背景上显示完全黑色的对象

边缘图：使用在MATLAB中实现的Canny边缘提取器将原始数据集中的图像转换为基于边缘的表示

纹理：48个自然彩色图像，纹理由动物的全宽贴片（皮肤、毛皮等）组成，具有相同物体的多次重复的图像组成

### Stylized-ImageNet

![](http://thorraysjtu.github.io/img/20200225/SIN.png)

从图片中剥离原始纹理，并通过样式转移，将其替换为随机选择的绘画风格，构建一个新的数据集（Stylized-ImageNet，SIN）

## 实验

![](http://thorraysjtu.github.io/img/20200225/Result.png)

CNN和人都可以正确的识别物体和纹理图片，对于sihouette和edge，CNN的识别精度远低于人类的精确度，表明人类观察者对具有很少或者没有纹理信息的图像可以处理的更好。人对形状类别的反应表现出明显偏向，而CNN与这种模式是相反的。

![](http://thorraysjtu.github.io/img/20200225/Result2.png)

通过SIN数据集，在ResNet-50上进行训练，用随机选择的艺术绘画的无信息风格取代了与物体相关的局部纹理信息，较纹理偏置有了更高的预测准确率。

**Good Idea**

局部纹理容易被各种噪声（包括现实世界中的那些噪声，例如雨和雪）扭曲，但是对象形状保持相对稳定。如果网络可以偏向于对于物体形状的识别，在一定程度上可以减少一些噪音带来的影响。
