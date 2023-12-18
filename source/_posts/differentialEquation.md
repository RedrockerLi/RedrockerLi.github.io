---
title: 一些微分方程的解法
date: 2023-12-18 16:31:06
tags: 
    - 数学
    - 傅里叶变换 
excerpt: 学的好混乱。
---
学数学物理方程学的有一点乱，在这里整理一下。

# 基本问题
主要研究下列几种方程。
## 波动方程
$$
\frac{\partial^2u}{\partial t^2}-a^2\frac{\partial^2u}{\partial x^2}=0
$$
$$
\frac{\partial^2u}{\partial t^2}-a^2\nabla^2u=\frac{f}{\rho}
$$
其中$f/\rho$为单位质量受到的外力$a=\sqrt{T/\rho}是弦振动传播速率$。
## 热传导方程
$$
\frac{\partial u}{\partial t}-\frac{k}{\rho c} \nabla^2u=\frac{F(x,y,z,t)}{\rho c}
$$
$F$为单位时间单位体积产生的热量。

**微观角度：**
$$
\frac{\partial u}{\partial t}-D\nabla^2=f(x,y,z,t)
$$
$D$为扩散率，$f$为单位时间单位体积分子产率。
## 稳定问题
稳定指温度达到稳定。

**Poisson方程：**
$$
\nabla^2u=\frac{F}{k}
$$
**Laplace方程：**
$$
\nabla^2u=0
$$
# 定解条件及适定性
## 定解条件
初始条件：完全描写初始时刻介质内部以及边界上任意一点的状态。

边界条件：完全描述边界上各点在任意时刻的状态。就未知函数而言是线性的。
>第一类：给出边界上各点函数值。
>
>第二类：给出边界上各点的法向微商值。
>
>第三类：给出边界上各点的函数值与法向微商值之间的线性关系。