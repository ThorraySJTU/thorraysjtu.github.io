---
layout: post
title: Mac下anaconda的安装
category: Tips
tags: [Tips]
---

# Mac下ananconda的安装及简单使用

anaconda可以看作Python的集成安装，安装它后默认安装了python、IPython、集成开发环境Spyder和众多的包和模块，安装后可以通过conda install 在指定的环境下安装更多的包和模块。

1. 下载适用的[安装包](https://www.anaconda.com/download/#macos)

2. 下载完成后双击安装

3.  安装成功，打开anaconda

   Terminal中输入conda是否成功

   ​		**否**：检查环境变量 Terminal中输入`sudo vi ~/.bash_profile`

   ​		环境变量中没有conda，需手动添加

   ​		`export PATH="/Users/anaconda3/bin:$PATH"`($PATH是anaconda的路径)

   ​		刷新环境变量 `source ~/.bash_profile`

   ​		验证 `conda list`

- [参考链接](https://docs.anaconda.com/anaconda/install/mac-os/)

