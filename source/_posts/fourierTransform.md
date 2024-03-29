---
title: 傅里叶变换
date: 2023-11-28 00:58:00
tags: 
    - 数学
    - 信号与系统
    - 傅里叶变换
mathjax: true
excerpt: 怎么到处都在用傅里叶。
---
# 定义

如果$f(x)$定义在$-\infty<x<+\infty$上且不是周期函数，则不能展开为傅里叶级数。显然，这种函数是普遍存在且有研究价值的。

为了解决这个问题，我们将$f(x)$看作周期函数$g(x)$的周期$2l\rightarrow\infty$时的极限情况。此时$g(x)$的傅里叶级数展开式
$$
g(x)=a_0+\sum\limits_{k=1}^{\infty}(a_k\cos{\frac{k\pi x}{l}}+b_k\sin{\frac{k\pi x}{l}})
$$
在$l\rightarrow\infty$时的极限情况就是$f(x)$的傅里叶展开。
$$
\begin{aligned}
f(x)&=\int_0^\infty\left[\frac{1}{\pi}\int_{-\infty}^\infty f(\xi)\cos{\omega\xi}\mathrm{d}\xi\right]\cos{\omega x}\mathrm{d}\omega+\int_0^\infty\left[\frac{1}{\pi}\int_{-\infty}^\infty f(\xi)\sin{\omega\xi}\mathrm{d}\xi\right]\sin{\omega x}\mathrm{d}\omega \\
&=\int_0^\infty A(\omega)\cos(\omega x)\mathrm{d}\omega+\int_0^\infty B(\omega)\sin(\omega x)\mathrm{d}\omega \\
&=\int_0^\infty C(\omega)\cos{[\omega x-\varphi(\omega)]}\mathrm{d}\omega
\end{aligned}\tag{1.1}
$$
其中
$$
\begin{aligned}
A(\omega)&=\frac{1}{\pi}\int_{-\infty}^\infty f(\xi)\cos{\omega\xi}\mathrm{d}\xi \\
B(\omega)&=\frac{1}{\pi}\int_{-\infty}^\infty f(\xi)\sin{\omega\xi}\mathrm{d}\xi \\
C(\omega)&=\{[A(\omega)]^2+[B(\omega)]^2\}^\frac{1}{2}\\
\varphi(x)&=\arctan{[B(\omega)/A(\omega)]}
\end{aligned}\tag{1.2}
$$

 $(1.1)$的积分为傅里叶积分，$(1.2)$前两式为傅里叶变换式，$C(\omega)$为振幅谱，$\varphi(x)$为相位谱。



# 存在性

$f(x)$在$(-\infty,+\infty)$上满足

1. $f(x)$在任一有限区间上满足狄里希利条件；
2. $f(x)$在$(-\infty,+\infty)$绝对可积；

> 1. $f(x)$在有限的区间内，只有有限个第一类间断点和有限个极大值和极小值。
> 2. $\int_{-\infty}^\infty |f(x)|\mathrm{d}x$收敛。

# 计算方法

## 复数形式

利用欧拉公式可以得到
$$
\begin{aligned}
f(x)&=\int_0^\infty \frac{1}{2}[A(\omega)-iB(\omega)]e^{i\omega x}\mathrm{d}\omega+\int_0^\infty \frac{1}{2}[A(\omega)+iB(\omega)]e^{-i\omega x}\mathrm{d}\omega \\
&=\int_0^\infty \frac{1}{2}[A(\omega)-iB(\omega)]e^{i\omega x}\mathrm{d}\omega+\int^0_\infty \frac{1}{2}[A(|\omega|)+iB(|\omega|)]e^{i\omega x}\mathrm{d}\omega \\
&=\int_{-\infty}^\infty F(\omega)e^{i\omega x}\mathrm{d}x \\
\\
F(\omega)&=\left\{ 
\begin{aligned}
&\frac{1}{2}[A(\omega)-iB(\omega)]\qquad\qquad&(\omega\ge0)\\
&\frac{1}{2}[A(|\omega|)+iB(|\omega|)]&(\omega<0)
\end{aligned}
\right.\\
&=\frac{1}{2\pi}\int_{-\infty}^\infty f(x)[e^{i\omega x}]^*\mathrm{d}x
\end{aligned}
$$
写成对称形式
$$
\begin{aligned}
f(x)&=\frac{1}{\sqrt{2\pi}}\int_{-\infty}^\infty F(\omega)e^{i\omega x}\mathrm{d}x=\mathcal{F}^{-1}[F(\omega)] \\
\\
F(\omega)&=\frac{1}{\sqrt{2\pi}}\int_{-\infty}^\infty f(x)[e^{i\omega x}]^*\mathrm{d}x=\mathcal{F}[f(x)]
\end{aligned}
$$

> 对于一个周期信号
> $$
> \begin{aligned}
> x(t)&=\sum\limits_{k=-\infty}^\infty a_ke^{j\omega _0t}
> \end{aligned}
> $$
> $\{a_k\}$称为$x(t)$的频谱系数

## 基本性质

- 导数定理: $\mathcal{F}[f'(x)]=i\omega F(\omega)$
- 积分定理: $\mathcal{F} \left[\int^{(x)}f(\xi)\mathcal{d}\xi\right]=\frac{1}{i\omega}F(\omega)$
- 相似性定理: $\mathcal{F}[f(ax)]=\frac{1}{a} F[\frac{\omega}{a}]$
- 延迟定理: $\mathcal{F}[f(x-x_0)]=e^{-i\omega x_0}F(\omega)$
- 位移定理: $\mathcal{F}[e^{i\omega_0x}f(x)]=F(\omega-\omega_0)$
- 卷积定理:$\mathcal{F}[f_1(x)*f_2(x)]=\sqrt{2\pi} F_1(\omega)F_2(\omega)$ 
- 微分定理:$F[x\cdot f(x)](\omega)=iF'[f(x)](\omega)$
> 系数和选定的公式前系数有关。

## $\delta$函数

### 定义

为了描述质点、点电荷、瞬时力等集中于某一点/瞬间的抽象模型，引入
$$
\delta (x)=\left\{
\begin{aligned}
&0\qquad \qquad(x\ne0)\\
&\infty \qquad \qquad(x=0)
\end{aligned} \right.
\\ 
\int_a^b \delta(x)\mathrm{d}x=\left\{
\begin{aligned}
&0\qquad \qquad(ab>0)\\
&1 \qquad \qquad(ab<0)\\
&\frac{1}{2}\qquad \qquad(ab=0)
\end{aligned} \right.
$$

### 性质

1. 本身是偶函数。
2. 可定义$H(x)=\int_{-\infty}^x \delta(t)\mathrm{d}t$是阶跃函数。
3. $\int_{-\infty}^\infty f(\tau)\delta(\tau-t_0)\mathrm{d}\tau=f(t_o)$被称为**挑选性**
4. 如果$\varphi(x)=0$的实根$x_k$全是单根，则$\delta(\varphi(x))=\sum\limits_k\frac{\delta(x-x_k)}{|\varphi'(x_k)|}$
5. $\delta (x)=\frac{1}{2\pi}\int_{-\infty}^\infty e^{i\omega x}\mathrm{d}\omega$
6. $\mathcal{F}[H(x)]=\frac{1}{2}\delta(\omega)-\frac{1}{2\pi}\mathcal(F)\frac{1}{\omega}$

<!-- ## 需要记忆的特殊函数
$$
\begin{aligned}
&F[\sin{ax}](\omega)=\pi i[\delta(\omega+a)-\delta(\omega-a)]\\
&F[e^{-x^2}](\omega)=\sqrt{\pi}e^{-\frac{\omega^2}{4}}
\end{aligned}
$$ -->

## DFT

## FFT

# 用途

## 香农采样定理
为了不失真地恢复模拟信号，采样频率应该大于等于模拟信号频谱中最高频率的2倍。
### 数学证明
设$f$在$\mathbb{R}$上连续，且$f\in L^1(\mathbb{R})$;进一步，若存在一定的常数$c$,当$|\omega|>c$时，$\hat{f}(\omega)=0$,那么下列采样公式成立。
$$
f(t)=\sum\limits _{n\in \mathbb{Z}}f(\frac{n\pi}{c})\frac{\sin{(ct-n\pi)}}{ct-n\pi}
$$
证明：由傅里叶反演公式
$$
f(t)=\frac{1}{\sqrt{2\pi}}\int_{-\infty}^\infty\hat{f}(\omega)e^{i\omega t}\mathrm{d}\omega=\frac{1}{\sqrt{2\pi}}\int_{-c}^c\hat{f}(\omega)e^{i\omega t}\mathrm{d}\omega
$$
在有限区间$(-c,c)$上引入函数
$$
g(\omega)=\frac{c}{\sqrt{\pi}}\hat{f}(\omega)\qquad\qquad |\omega|<c
$$
将g扩展为$\mathbb{R}$上以$2c$为周期的周期函数，则有傅里叶级数展开
$$
\begin{aligned}
g(\omega)&\sim\sum\limits_{n\in\mathbb{Z}}c_n(g)e^{i(n\pi/c)\omega} \\
c_n(g)&=\frac{1}{2c}\int_{-c}^cg(\omega)e^{-i(n\pi/c)\omega}\mathrm{d}\omega \\
&=\frac{1}{2\sqrt{\pi}}\int_{-c}^c\hat{f}(\omega)e^{-i(n\pi/c)\omega}\mathrm{d}\omega=f\left(-\frac{n\pi}{c}\right)
\end{aligned}
$$
进一步，考虑$(-c,c)$中的函数
$$
h(\omega)=e^{-it\omega}\qquad\qquad |\omega|<c
$$
类似的，可得
$$
\begin{aligned}
h(\omega)&\sim\sum\limits_{n\in\mathbb{Z}}c_n(h)e^{i(n\pi/c)\omega} \\
c_n(h)&=\frac{1}{2c}\int_{-c}^ch(\omega)e^{-i(n\pi/c)\omega}\mathrm{d}\omega \\
&=\frac{1}{2c}\int_{-c}^ce^{-it\omega}e^{-i(n\pi/c)\omega}\mathrm{d}\omega \\
&=\frac{1}{2c}\int_{-c}^ce^{-i(ct+n\pi)\frac{\omega}{c}}c\mathrm{d}(\frac{\omega}{c})\\
&=\left.\frac{1}{2}\frac{1}{-i(ct+n\pi)}e^{-i(ct+n\pi)t}\right|^{1}_{-1} \\
&=\frac{\sin{(ct+n\pi)}}{ct+n\pi}
\end{aligned}
$$
> Parseval等式
>
> $\left\{\phi_j\right\}_{j=1}^\infty$完备当且仅当$\forall\mathcal{u},\mathcal{v}\in\mathcal{V}$,有Parseval等式
> $$\left<\mathcal{u},\mathcal{v}\right>=\sum\limits_{j=1}^\infty\left<\mathcal{u},\phi_j\right>\left<\phi_j,\mathcal{v}\right> $$

$$
\left<h,g\right>=\int_{-c}^{c}g(\omega)\overline{h(\omega)}\mathrm{d}\omega=\sum\limits_{n\in\mathbb{Z}}\left<\mathcal{h},\phi_n\right>\left<\phi_n,g\right> \\
\begin{aligned}
f(t)=\frac{1}{2\sqrt{\pi}}\int_{-c}^c\hat{f}(\omega)e^{i\omega t}\mathrm{d}\omega
&=\frac{1}{2c}\int_{-c}^{c}g(\omega)\overline{h(\omega)}\mathrm{d}\omega\\&=\sum\limits_{n\in\mathbb{Z}}\overline{c_n(h)}c_n(g)\\
&=\sum\limits_{n\in\mathbb{Z}}f\left(\frac{n\pi}{c}\right)\frac{\sin{(ct-n\pi)}}{ct-n\pi}\\
\end{aligned}
$$
所以采样频率为$\frac{2\pi}{\pi/c}=2c$.