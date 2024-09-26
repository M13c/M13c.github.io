---
title: Diffusion数学推导
tags: []
mathjax: true
date: 2024-09-26 15:57:25
categories: Diffusion
excerpt: 从VAE开始的Diffusion的数学推导
---

## 生成模型

基于$Likelihood$的生成模型：给定一个数据集$x_D$，训练使得模型最大化$Likelihood p_\phi(x_D)$

{% asset_img 1.png %}

## VAE
> Variational AutoEncoder

{% asset_img 2.png %}

Encoder: $q(z|x)$
Decoder: $p(x|z)$

对于潜在变量(latent variable) z 的分布$p(z)$一般选择高斯分布$N (z; 0, I)$


### 优化目标

目标：使得建模的$p_\phi(x)$趋于真实分布 $p(x)$

+ 但是直接建模 $p(x)$ 是非常困难的。
    $Eg:$
    (1)边缘化$z$  
    {% asset_img 3.png %}
    (2)链式法则
    {% asset_img 4.png %}

+ 引出ELBO
    表达式如下：
    {% asset_img 5.png %}
    其中，$q_\phi(z|x)$ 是一个近似变分分布，其参数为我们寻求优化的 $\phi$
    
    同时和$p(x)$满足关系
    {% asset_img 6.png %}
    + 证明：
        （1）琴声不等式
        {% asset_img 7.png %}
        （2） KL散度
        {% asset_img 8.png %}
        
+ 优化目标的转变
    **优化VAE，其实就是最大化ELBO**
    
    {% asset_img 9.png %}
    解释：VAE是通过decoder和encoder分别拟合$p(x|z)$和$q(z|x)$，来间接拟合  $p_\phi(x) \rightarrow p(x)$。
    上式KL散度反映的就是encoder对真实$p(z|x)$的拟合程度，要最小化它。但是直接优化KL散度不现实，因为不知道$p(z|x)$这个$ground truth$。
    $logp(x)$是真实的数据分布，对于给定数据集，是一个固定的值，与模型无关。
    所以**最小化KL散度等价于最大化ELBO**
    
+ ELBO的解释
    {% asset_img 10.png %}
    + 最大化$reconstruction term$: 让$decoder$能最大可能的从隐变量$z$生成原始数据x。
    + 最小化 $prior matching term$ ：让$encoder$ 将真实数据$x$映射到隐变量$z$后，$z$尽量能满足我们指定的分布，一般为 $N(z; 0, I)$
    + $Tips$: 如果把$Prior matching term$这项损失去掉，就是AE模型。没有P项，$z$没有趋向于正态分布，故AE的$z$分布未知，没法有效采样，所以也不能作为生成模型。
    
### VAE架构

{% asset_img 11.png %}
+ 图中公式：
    {% asset_img 13.png %}
    优化目标的均值项使用蒙特卡洛模拟采样
    {% asset_img 14.png %}

+ 重参数化
    {% asset_img 12.png %}
    
## MHVAE

> Markovian Hierarchical Variational AutoEncoder

{% asset_img 15.png %}
MHVAE 就是级联的马尔可夫VAE链

+ $Encoder$和$Decoder$
    {% asset_img 16.png %}

+ $ELBO$
    {% asset_img 17.png %}
    
## VDM

> Variational Diffusion Model

{% asset_img 18.png %}

### 从MHVAE到VDM

+ 三个限制
    1. 数据x和所有隐变量z的维度相同
    {% asset_img 19.png %}
    2. 每个时间步的潜在编码器的结构都没有被学习；它被预先定义为线性高斯模型。换句话说，它是一个以前一个时间步的输出为中心的高斯分布
    {% asset_img 20.png %}
    其中 $0 < \alpha_t < 1$
    3. 最终时间步长 T 的潜在分布是标准高斯分布
    {% asset_img 21.png %}
    

### ELBO的解释
{% asset_img 22.png %}
{% asset_img 25.png %}
粉色和绿色箭头指的Consisitency term的KL散度
+ Prior matching term最后一步推导
    {% asset_img 23.png %}
+ Consisitency term最后一步推导
    {% asset_img 24.png %}
+ 问题与改进

    最后的Consisitency中需要对两个变量进行蒙特卡洛模拟，可能有较大的方差。
    采用贝叶斯定理
    {% asset_img 26.png %}
    
    重写ELDBO
    {% asset_img 27.png %}
    {% asset_img 28.png %}

### $Ground Truth$

如何求出去噪项的$q(x_{t-1}|x_t, x_0)$

答：使用贝叶斯
{% asset_img 29.png %}

+ 递推
{% asset_img 30.png %}

+ 代入
{% asset_img 31.png %}

+ 特性

    {% asset_img 32.png %}
    1. 是个高斯分布
    2. 方差$\sum_q(t)$只和$\alpha_t$有关，故是个常数
    3. 均值$\mu_q(x_t, x_0)$是关于$x_t, x_0$的函数
    
### Loss

+ 拟合均值
    {% asset_img 33.png %}
    其中两个高斯分布的KL散度为
    {% asset_img 34.png %}
    
    **最小化KL散度等价于拟合$q(x_{t-1}|x_t, x_0)$的均值**
    
+ 拟合$x_0$

    均值的表达式
    {% asset_img 35.png %}
    {% asset_img 36.png %}
    
    拟合$x_0$的推导
    {% asset_img 37.png %}
    
    **最小化KL散度等价于根据$x_t$和$t$拟合$x_0$**
    
+ 拟合噪声

    用噪声表达$x_0$
    {% asset_img 38.png %}
    
    待入
    {% asset_img 39.png %}
    {% asset_img 40.png %}
    
    拟合噪声的推导
    {% asset_img 41.png %}
    
### Inference
{% asset_img 42.png %}


{% notel blue 参考 %}
[Understanding Diffusion Models: A Unified Perspective](https://arxiv.org/abs/2208.11970)
[Bilibili](https://www.bilibili.com/video/BV1Ax4y1v7CY)
{% endnotel %}