---
title: Google Colaboratory的配置和使用
date: 2021-01-07 00:35:54
categories: 码农笔记
toc: true
tags:
  - Google Colab
---

## 0. 前言

最近一直在炼丹，之前老师租的服务器上千块钱一个月，显卡也很一般，Google完全免费的Colaboratory，通常给到的GPU资源是Tesla T4，TPU也是能免费用的，8个核，相当于8个GPU分布式训练，快的飞起，就是用起来程序上很麻烦。开通Colab Pro每月就9.9刀，同时可以多开，拿到的GPU一般的Tesla V100和P100，相比T4快了不少，V系类算力更高些。

谷歌在环境方便做了很好的适配，兼容性很高，用了一段时间之后，只能说Google一次又一次让我感到震惊，友好的UI和全局中文，添加了很多方便的可视化操作，从使用体验上来说就已经无敌了，这就是资本的力量吗。

当然Colab有使用限制，每次运行最多12个小时，不过我Pro三个会话好像通常不到6个小时，做些小模型小数据集的实验还是比自己的服务器划算一些.

<!--more-->

## 1. 创建笔记本

- **新建Google Colaboratory**

    如果Colaboratory不存在，转到页面[https://colab.research.google.com/notebooks/welcome.ipynb](https://colab.research.google.com/notebooks/welcome.ipynb)

    点击左上角的“复制到云盘”，重新进入自己的云盘新建选项中就会出现Colaboratory。

    然后可以新建ipynb文件，也就和jupyter notebook类似。

    在ipynb中可以对当控制台使用，在指令面前加"!"就行。

## 2. 选择GPU/TPU

在“修改”=>“笔记本设置” 中可以选择GPU，然后在右边有连接选项

检测GPU情况：

```python
# 检测GPU情况
import tensorflow as tf
tf.test.gpu_device_name()
```

查看显卡信息：

```python
!nvidia-smi
```

![Google, yyds](https://wx1.sinaimg.cn/mw2000/0069EgM5gy1grzln8w6ujj30mo0dndgh.jpg)

## 3. 挂载Google Drive

在左边的控制栏中有直接的挂载Drive的选项，也可以通过`drive.mount`添加。

```python
from google.colab import drive
drive.mount('/content/drive/')
```

## 4. 更改运行目录

更改到目录之后可以直接`!python youcode.py`运行python程序。

```python
import os
os.chdir("/content/drive/MyDrive/MyCode")
```

## 5. 切换tensorflow环境

```python
%tensorflow_version 1.x
```

## 6. Google，暂时的神
