---
title: PnP解算初步
date: 2024-7-13 13:38:44
updated: 2024-7-13 13:38:44
tags:  
    - Computer Vision
excerpt: 双目VS单目
mathjax: true
---

# 一笔经济账

如果想要通过相机获得深度信息，最简单方便的是使用Intel realsence.相机自带硬件加速， 拥有详细的文档和库，使用起来非常方便。它的深景速率有90fps，看上去不错，但画面其实只有60fps。同样的价格（2000+人民币）可以买到3台相近规格的工业相机。当然这笔账并不完善，并没有考虑使用场景和背后的计算设备。

# PnP解算是什么

PnP（Perspective-n-Points）问题的正式提出是在20世纪80年代中期，目的是解决在已知一些三维点的情况下，通过单张二维图像确定摄像机姿态的难题。初期的PnP求解方法主要基于线性代数和几何变换，提出了一些基础的算法如DLT（Direct Linear Transform）。这些方法虽然在理论上可行，但对噪声敏感，且计算效率较低。

21世纪，随着数学和计算方法的进步，PnP算法得到了显著优化。例如，Lepetit等人在2008年提出了EPnP（Efficient PnP）算法，通过引入虚拟控制点和改进的求解策略，显著提高了PnP问题的求解效率和鲁棒性。与此同时，RANSAC（Random Sample Consensus）等鲁棒估计方法的引入，使得PnP算法在处理实际应用中的噪声和异常值时更加稳定可靠。

# 相机成像过程

## 四个坐标系

### 世界坐标系

世界坐标系$(o_w-x_w-y_w-z_w)$不是一个有明确定义的坐标系，你可以任意指定一个在当前场景下固定不变的坐标系作为世界坐标系。

### 相机坐标系

相机坐标系$(o_c-x_c-y_c-z_c)$习惯上这样定义：以相机光心为坐标系原点；假设你在手持相机，那么你的右手边是x轴正方向，下边是y轴正方向，相机前方是z轴正方向，也就是说，拍摄的远处物体距离为正。

### 图像坐标系

图像坐标系$(o_i-x_i-y_i)$的原点就位于感光芯片的中心（如CMOS），x、y轴方向和相机坐标系x、y相同，不同的是图像坐标系是二维坐标系没有z轴。

### 像素坐标系

像素坐标系$(o_p-x_p-y_p)$也位于成像平面上，是图像坐标系通过平移和缩放的到的。值得注意的是不同软件或者库对于像素坐标$(u,v)$的定义可能不一样，使用前需要统一规定。

## 相机内参

相机的内参描述了相机内部的几何属性，包含两部分：内参矩阵和畸变系数。

内参矩阵：$K$

$$
\begin{bmatrix}
  f_x & 0 & c_x \\
  0 & f_y &c_y  \\
  0 & 0 & 1
\end{bmatrix}
$$
其中$f_x$和$f_y$是焦距参数，$c_x$和$c_y$是图像平面的原点坐标。

它的作用是把坐标从相机坐标系转到像素坐标。
$$
\begin{bmatrix}
u\\
v\\
1
\end{bmatrix}
=\frac{1}{Z_c}
\begin{bmatrix}
  f_x & 0 & c_x \\
  0 & f_y &c_y  \\
  0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
X_c\\
Y_c\\
Z_c
\end{bmatrix}
$$
可以看到深度信息在变换中被消去了。

畸变系数：$D=[k_1,k_2,p_1,p_2,k_3]$

$k1,k_2,k_3$是径向畸变，描述沿着半径方向分布的畸变。

$p_1,p_2$是切向畸变，描述透镜和成像面不平行导致的畸变。

径向畸变模型：
$$
u_{distorted}=u(1+k_1r^2+k_2r^4+k_3r^3)\\
v_{distorted}=v(1+k_1r^2+k_2r^4+k_3r^3)\\
$$
切向畸变最方便的方法：换镜头。

更多资料：[常用相机投影及畸变模型（针孔|广角|鱼眼）_相机畸变模型](https://blog.csdn.net/qq_28087491/article/details/107965151)

## 相机外参

外参矩阵描述的是世界坐标系下坐标与相机坐标系下坐标的转换关系。
$$
\begin{bmatrix}
X_c\\
Y_c\\
Z_c\\
\end{bmatrix}=
\begin{bmatrix}
R_{3\times3}&T_{3\times1}
\end{bmatrix}
\begin{bmatrix}
X_w\\
Y_w\\
Z_w\\
1
\end{bmatrix}
$$
$R$描述旋转，$T$描述平移。

求出外参就相当于确定了相机的姿态。

# 算法实现

下面我们看看PnP解算不同算法的细节。

## 直接线性变换(Direct Linear Transform，DLT)

**已知条件：**世界坐标系3D点的坐标，像素坐标系中对应点的坐标（称为特征点）和相机的内参矩阵。

根据成像模型：
$$
Z_c
\begin{bmatrix}
u\\
v\\
1
\end{bmatrix}
=
\begin{bmatrix}
  f_x & 0 & c_x \\
  0 & f_y &c_y  \\
  0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
R_{3\times3}&T_{3\times1}
\end{bmatrix}
\begin{bmatrix}
X_w\\
Y_w\\
Z_w\\
1
\end{bmatrix}
$$
展开
$$
Z_c
\begin{bmatrix}
u\\
v\\
1
\end{bmatrix}
=
A_{3\times4}
\begin{bmatrix}
X_w\\
Y_w\\
Z_w\\
1
\end{bmatrix}
$$
先求出A矩阵，又因为相机内参已知，然后就可以求出外参。

对于一组匹配点，可以得到三个方程，消去$Z_c$后可以得到两个方程，有因为$A$有12个元素，所以需要6组对应点才能解这个方程组。

## EPnP

原论文：[EPnP: An Accurate O(n) Solution to the PnP Problem ](https://link.springer.com/article/10.1007/s11263-008-0152-6)

EPnP的复杂度是$O(n)$，非常高效。

### 步骤

1. 在世界坐标系下确定4个控制点$c_j^w,j=1,2,3,4$.选取方法是：取所有特征点的质心$c_1^w$为原点，再通过主成分分析确定另外的三个点。
2. 已知特征点在世界坐标系的坐标$P_i^w$,以及控制点在世界坐标系下的坐标（第一步计算得出），计算权重因子$\alpha_{ij}$.
3. 计算四个控制点在相机坐标系下的坐标。
4. 计算相机外参

### 控制点的选择

第一个控制点：$c_1^w=\frac{1}{n}\sum\limits_{i=1}^nP_i^w$

对特征点重心化得到矩阵A。
$$
\begin{bmatrix}
{P_1^w}^T-{c_1^w}^T\\
\dots\\
{P_n^w}^T-{c_1^w}^T\\
\end{bmatrix}
$$
$A^TA$的三个特征值为$\lambda_1,\lambda_2,\lambda_3$,对应的特征向量为$v_1,v_2,v_3$.

剩余的三个控制点的坐标为：
$$
\left\{
\begin{aligned}
&c_2^w=c_1^w+\sqrt{\lambda_1}v_1\\
&c_3^w=c_1^w+\sqrt{\lambda_2}v_2\\
&c_4^w=c_1^w+\sqrt{\lambda_3}v_3
\end{aligned}
\right.
$$

### 计算权重因子

特征点的坐标可以表示为控制点坐标的加权和：
$$
P_i^w=\sum\limits_{j=1}^4\alpha_{ij}c_j^w\qquad\sum\limits_{j=1}^4\alpha_{ij}=1
$$
一旦控制点确定，$\alpha_{ij}$也随之确定。

在相机坐标系中也存在相同的加权关系：
$$
P_i^c=\sum\limits_{j=1}^4\alpha_{ij}c_j^c\qquad\sum\limits_{j=1}^4\alpha_{ij}=1
$$
通过乘相机外参矩阵可证。

对每一个固定的$i$,上面是四个方程四个未知数（有三个坐标），这也是第一步选四个控制点的原因。

### 计算控制点在相机坐标系下的坐标

设$K$是摄像头的内参矩阵，$q_i$是像素坐标系中特征点$P_i$的对应点。

根据成像模型：
$$
(\sum\limits_{j=1}^4\alpha_{ij}Z_j^c)
\begin{bmatrix}
u_i\\
v_i\\
1
\end{bmatrix}=
K\sum_{j=1}^4\alpha_{ij}
\begin{bmatrix}
X_j^c\\
Y_j^c\\
Z_j^c
\end{bmatrix}
$$
未知量是四个控制点在相机坐标系下的坐标，一共12个参数。同样的，一个点可以列2个方程。

得到一个线性方程：
$$
M_{2n\times12}X_{12\times1}=0
$$
 $X$是待求的12个参数。

对$M^TM$进行特征值分解，得到N个零特征值对应的特征向量$v_i$,则
$$
X=\sum\limits_{i=1}^N\beta_i v_i
$$
然后引入相对位置约束条件$||c_i^c-c_j^c||=||c_i^w-c_i^w||$,得到$C_4^2$个方程，根据不同的$N=1，2，3，4$可以解出对应的$\beta_1\sim\beta_4$的初始值。最后使用最小二乘优化解出精确值。

## 计算相机外参

得到$c_i^c$后，可以所有的3D点在摄像机坐标系下的坐标，得到所有的3D-3D匹配，把问题转化为了已知的ICP问题。

# Random sample consensus

RANSAC的核心思想是随机选择数据中的一小部分样本，并根据这些样本拟合一个模型。然后，通过计算其他数据点到该模型的距离，并将距离小于一定阈值的数据点划分为内点，而距离大于阈值的数据点则划分为外点。重复此过程多次，并选择具有最多内点的模型作为最终的估计结果。

RANSAC算法的优点在于它对于大量异常值和噪声的数据有较好的鲁棒性。它可以估计出包含异常值的数据集中的准确模型，并且不要求事先知道异常值的数量，这种算法常被用于处理具有离群点或噪声的数据。

# 参考文献

[相机内参、外参、畸变系数简介](https://blog.csdn.net/weixin_44368569/article/details/130414934)

[PnP算法详解（超详细公式推导](https://www.jianshu.com/p/a35fa8ac0803)
