---
title: Python之包管理工具anaconda安装
date: 2020-10-25 11:55:09
tags:
categories:
  - Python
---

## 什么是anaconda
> Conda 是一个开源的软件包管理系统和环境管理系统，用于安装多个版本的软件包及其依赖关系，并在它们之间轻松切换。 Conda 是为 Python 程序创建的，适用于 Linux，OS X 和Windows，也可以打包和分发其他软件

用过maven和grade的同学应该能够理解，其实就是一个依赖包管理工具
将我们平时去网上找包的下载在导入的工作进行省却，只需要一条命令即可

## anaconda和miniconda
他们之间的最大的区别就是一个带有常用的库一个是精简版，只有conda工具和python环境

## 安装
1. 这里我们选择的是anaconda的安装，我们先去官网下载安装包
[https://www.anaconda.com/products/individual](https://www.anaconda.com/products/individual)
这是miniconda的下载地址[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)
这里大家根据自己的需求来下载，我这里下载的是python版本3.7的
![image.png](https://qiniu.xiaosm.cn/blog/image_1593524392845.png-blogshuiyin)

2. 下载完成运行安装，一直下一步就好了这里没啥好说的
3. 安装完成后最后一个是问你需不需要了解“Anaconda云”和“Anaconda支持”，不需要的话不勾选就好了，至此我们的python就装好了
运行装好的**Anaconda Prompt (anaconda3)**
``` shell
conda -V // 查看版本
conda list // 查看已安装的库
python // 执行python脚本
```
4. 每次都要运行软件非常麻烦，所以我们将conda添加进环境变量
**D:\Environment\anaconda3**
**D:\Environment\anaconda3\Scripts**
具体的路径请根据你的安装路径来定
