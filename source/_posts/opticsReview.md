---
title: 光学复习-I
date: 2023-11-28 21:15:29
tags:
    - 物理
excerpt: 光学期中考试复习
mathjax: true
---
# 光速

$v=\frac{1}{\sqrt{\epsilon_0\mu_0\epsilon_r\mu_r}}$	 $c=\frac{1}{\sqrt{\epsilon_0\mu_0}}$	$v=\frac{c}{n}$

# 能量

## 能流密度（坡印亭矢量）

$S=|E\times H|=\sqrt{\frac{\epsilon_0 \epsilon_r}{\mu_0 \mu_r}}E_0^2\cos{[\omega+(t-\frac{x}{v}+\varphi)]}$

## 辐照度（光强）

$I=\bar{S}=\frac{1}{T}\int _0^TS\mathrm{d}t=\frac{1}{2}\sqrt{\frac{\epsilon_0 \epsilon_r}{\mu_0 \mu_r}}E_0^2=\frac{n}{2c\mu _0}E_0^2(\mu _r\approx1)$

> 同一种介质$I=E_0^2$
>
> 不同种介质$I=nE_0^2$

# 简谐波的数学表达式

## 一维简谐波

$U=A\cos[\omega(t-\frac{x}{v})+\varphi]$

> 传播方向：x沿传播方向变化，带来的相位变化相当于t减小

## 三维简谐波

平面波：$U=A\cos(\omega t-\vec{k} \cdot \vec{r} )$

球面波：$U(\vec{r},t)=\frac{A}{|\vec{r}|}\cos(\omega t-\vec{k} \cdot \vec{r} )$

- 傍轴条件/振幅条件

> $z^2\gg \sqrt{x^2+y^2}$

- 远场条件/相位条件

  > $z\lambda\gg \sqrt{x^2+y^2}$

## 复数表示

$\tilde{U}(P,t)=Ae^{i\phi(P)}\cdot e^{-i\omega t}=\tilde{U}(P)e^{-i\omega t}$

## 波前函数

复振幅中的$P$加上 在某一个平面上的约束，求出波前的复振幅分布即为波前函数。

# 光的横波性

偏振度：$P=\frac{I_{max}-I_{min}}{I_{max}+I_{min}}\in[0,1]$

# 费马原理

## 光程

$L=\sum\limits_{i=1}^m n_is_i=\int _Q^P n \mathrm{d}s$

## $\delta L(l)=0$

极大、极小、稳定均有可能。以椭圆焦点出射为例，

> 在椭圆面反射：稳定。
>
> 在和椭圆面相切的凹面镜的切点处反射：极大。
>
> 在和椭圆面相切的凸面镜的切点处反射：极小。

透镜等光程：保证干涉衍射能使用透镜而不改变结果。

# 几何光学

## 成立的条件

- 光学系统的尺度远大于光波的波长
- 介质各向同性
- 光强不是很大

## 成像

- 等光程

> - 反射
>
> >等光程面：椭球面，抛物面，双曲面，平面
>
> - 折射
>
> >齐明点：球面(半径为$r$)折射时距球心$nr$和$\frac{1}{n}r$
> >
> >平面折射能成像，不能严格成像

- 一一对应可对易
- 同心光束不变

## 傍轴成像

同心光束经过球面光具没有同心性，但傍轴光线接近同心

$\frac{n'}{s'}+\frac{n}{s}=\frac{n'-n}{r}$

$\frac{f'}{s'}+\frac{f}{s}=1$

## 薄透镜成像

$
\left\{
\begin{aligned}
&f=\frac{f_1f_2}{f_1'+f_2}=\frac{n}{\frac{n_L-n}{r_1}+\frac{n'-n_L}{r_2}} \\
&f'=\frac{f_1'f_2'}{f_1'+f_2}=\frac{n'}{\frac{n_L-n}{r_1}+\frac{n'-n_L}{r_2}} \\
\end{aligned}
\right.
$

空气中：$f=f'=\frac{1}{(n_L-1)(\frac{1}{r_1}-\frac{1}{r_2})}$

$f=f'$时：$\frac{1}{s'}+\frac{1}{s}=\frac{1}{f}$

## 牛顿公式

$
\left\{
\begin{aligned}
&s=x+f\\
&s'=x'+f' \\
&xx'=ff'\\
&V=-\frac{f}{x}\\
&V=-\frac{s'}{s}\qquad(f=f')
\end{aligned}
\right.
$

## 光焦度

$P=\frac{n}{f}$

$P=P_1+P_2$

单位为$D$

$f=-50cm\qquad P=\frac{1}{-0.5m}=-2D$

## 光学仪器

$s_0$为明视距离

- 放大镜

> $f\ll s_0$
>
> $M=\frac{s_0}{f}$

- 显微镜($\Delta$为像方焦点和目镜焦点距离/筒长)

> $f_O<f_E\ll s_0$
>
> $f_O,f_E\ll \Delta$
>
> $M=V_OM_E=-\frac{\Delta}{f_O}\frac{s_0}{f_E}$

- 望远镜

> $f_E<f_O$
>
> 像方焦点和目镜焦点几乎重合
>
> $M=-\frac{f_0}{f_E}$

# 菲涅尔反射折射公式

## 坐标架

坐标架的$p$ $s$ $k$ 和实际分量的方向没有关系，只是一组基。

## 菲涅尔公式

- 反射


> $\tilde{E}_{1s}'=\frac{n_1\cos{i_1}-n_2\cos{i_2}}{n_1\cos{i_1}+n_2\cos{i_2}}\tilde{E}_{1s}=-\frac{\sin{(i_1-i_2)}}{\sin{(i_1+i_2)}}\tilde{E}_{1s}$
>
> $\tilde{E}_{1p}'=\frac{n_2\cos{i_1}-n_1\cos{i_2}}{n_2\cos{i_1}+n_1\cos{i_2}}\tilde{E}_{1p}=\frac{\tan{(i_1-i_2)}}{\tan{(i_1+i_2)}}\tilde{E}_{1p}$

- 折射


>$\tilde{E}_{2s}'=\frac{2n_1\cos{i_1}}{n_1\cos{i_1}+n_2\cos{i_2}}\tilde{E}_{2s}=\frac{2\cos{i_1}\sin{i_2}}{\sin{(i_1+i_2)}}\tilde{E}_{2s}$
>
>$\tilde{E}_{2p}'=\frac{2n_1\cos{i_1}}{n_2\cos{i_1}+n_1\cos{i_2}}\tilde{E}_{2p}=\frac{2\cos{i_1}\sin{i_2}}{\sin{(i_1+i_2)}\cos{(i_1-i_2)}}\tilde{E}_{2p}$

## 三种不同的反射率和透射率

下面只关注透射，反射使$n_1=n_2$即可

振幅：$\tilde{t}_p=\frac{\tilde{E}_{2p}}{\tilde{E}_{1p}}$

光强：$T_p=\frac{I_{2p}}{I_{1p}}=\frac{n_2}{n_1}|\tilde{t}_p|^2$

光功率$(W=IS)$：$\mathcal{T}_p=\frac{W_{2p}}{W_{1p}}=\frac{\cos{i_2}}{\cos{i_1}}T_p$

$\mathcal{R}_p+\mathcal{T}_p=1$	$\mathcal{R}_s+\mathcal{T}_s=1$

## 斯托克斯倒逆关系

$\tilde{r}^2+\tilde{t}\tilde{t}'=1$

$\tilde{r}'=-\tilde{r}$

对$p$和$s$分量均成立

## 相位变化

研究反射光，因为$\tilde{t}>0$

- $n_1<n_2$

> $\tilde{r}_s<0$恒成立
>
> $\tilde{r}_p$主要看$\tan{(i_1+i_2)}$的符号，转变点为布儒斯特角。

- $n_1>n_2$

> $i_C>i_B$:全反射临界角大于布儒斯特角
>
> $i_1>i_C$时，$\sin{i_2}>1$,$\cos{i_2}\&\tilde{r}_p\&\tilde{r}_s$为虚数,$|\tilde{r}|=1$
>
> $\delta_p=2\arctan{\frac{n_1}{n_2}\frac{\sqrt{(\frac{n_1}{n_2}\sin{i_1})^2-1}}{\cos{i_1}}}$
>
> $\delta_s=2\arctan{\frac{n_2}{n_1}\frac{\sqrt{(\frac{n_1}{n_2}\sin{i_1})^2-1}}{\cos{i_1}}}$

## 半波损失/跃变

- 正入射

> - $n_1 < n_2$
>
> >$\tilde{r}_s<0$,反射光$s$分量与坐标架相反
> >
> >其他三个分量与坐标架相同
>
> - $n_1 > n_2$
>
> > $\tilde{r}_p<0$,反射光$p$分量与坐标架相反
> >
> > 其他三个分量与坐标架相同

半波跃变是和入射光比较，所以仅$n_1 < n_2$时有半波跃变

- 掠入射

> - $n_1 < n_2$
>
> >反射光$p,s$分量均与坐标架相反，有半波跃变
>
> - $n_1 > n_2$
>
> >发生全反射，相位差不是$\pi$,不是半波跃变

- 一般斜入射

> 直接反射和折射一次再反射的光一定存在半波跃变，原因是在不发生全反射的情况下，直接反射和折射一次再反射的光在反射时的入射角在对应布儒斯特角的同侧，$n_1 < n_2$和$n_1 > n_2$两种情况下同一分量的相位差之和为$\pi$.
>
> 更一般的，要求$n_1<n_2>n_3$或$n_1>n_2<n_3$

## 反射、折射时的偏振

布儒斯特角为全偏振角/起偏角

# 全反射时的隐失波

解决的问题：全反射时介质2中的场强。

$\tilde{E}_2=\tilde{E}_{20}\cdot e^{-k'_{2z}z}\cdot e^{i(k_{2x}-\omega t)}$

其中$k_{2z}=ik_{2z}'=i\sqrt{(n_1\sin{i_1})^2-n_2^2}\cdot k_0$

## 穿透深度

定义为振幅衰减到原来的$\frac{1}{e}$时的空间距离。

$d=\frac{1}{k_{2z}'}$，为一个波长量级

## 特点

- 实波矢分量大于波矢总量
- 等相面和等幅面正交
- 存在能流，但穿过界面的能流时间平均值为0

## 实验观察

受抑全反射：在第二个棱镜中生成一行波。

## 应用

- 用薄介质膜传输棱镜全反射生成的隐失波。

- 近场扫描显微镜

# 相干叠加

## 三角

$U_1(P,t)=A_1\cos{[\omega t-\frac{2\pi r_1}{\lambda}+\varphi_1]}$

$U_2(P,t)=A_2\cos{[\omega t-\frac{2\pi r_2}{\lambda}+\varphi_2]}$

$A^2=A_1^2+A_2^2+2A_1A_2\cos{\delta}$

$\delta=\frac{2\pi}{\lambda}(r_2-r_1)-(\varphi_2-\varphi_1)$

> 可得出相干叠加的必要条件：
>
> - 频率相同
> - 振动方向一致，或有振动方向一致的平行分量
> - 具有稳定的相位差
>
> 为获得较高衬比度，追加两点：
>
> - 振幅尽量相等
> - 传播方向夹角不大

## 复振幅叠加

$\tilde{U}=\tilde{U}_1+\tilde{U}_2=A_1e^{i\phi_1}+A_2e^{i\phi_2}=Ae^{i\phi}$

$A^2=A_1^2+A_2^2+2A_1A_2\cos{(\phi_2-\phi_1)}$

$\tan{\phi}=\frac{A_1\sin{\phi_1}+A_2\sin(\phi_2)}{A_1\cos{\phi_1}+A_2\cos(\phi_2)}$

# 分波前干涉

## 杨氏干涉标准情况

$I(P)=I_1+I_2+2\sqrt{I_1I_2}\cos{\delta}$

$\delta=\frac{2\pi}{\lambda'}(r_2-r_1)=\frac{2\pi}{\frac{\lambda}{n}}(r_2-r_1)=\frac{2\pi}{\lambda}(nr_2-nr_1)=\frac{2\pi}{\lambda}\Delta$

$\Delta=nr_2-nr_1\approx n\frac{d}{D}x$

$d$为双孔之间的距离，$D$为幕到双孔的距离

光强分布：$I=2I_0(1+\cos{\frac{2\pi}{\lambda}\Delta})=4I_0\cos^2{\frac{\pi}{\lambda}\Delta}$

## 较一般情况

- 不同波长

> 0级重叠，其他各级错开。
>
> 无法辨认：$k\lambda_1=(k+1)\lambda_2$	$(\lambda_1>\lambda_2)$

- 双缝

> 等价于很多双孔，增强干涉效果

- 光源位置$(\varepsilon)$对干涉条纹的影响

> $\Delta=n_1\frac{d}{R}\varepsilon+n_2\frac{d}{D}x$
>
> 间距不变，整体下移$\Delta x'=\frac{n_1D}{n_2R}\varepsilon$

- 光源宽度$(b)$对干涉条纹的影响

> $\frac{n_1D}{n_2R}b\geq \frac{D}{n_2d}\lambda$	$\frac{n_1d}{R}b\ge\lambda$时不产生干涉条纹
>
> 其他情况衬比度下降

## 菲涅耳双面镜

光源在两块镜子上反射成的虚像等价于杨氏双缝干涉的缝。

## 菲涅尔双棱镜

两个顶角很小，底面相接的薄棱镜，实际由一块玻璃磨制而成。

折射成的虚像等价于杨氏双缝干涉的缝

## 劳埃德镜

一个平面镜，光源和一个反射成的虚像等价于杨氏双缝干涉的缝。

# 分振幅干涉

## 光程差

$\Delta=2nh\cos{r}+\frac{\lambda}{2}$

$r$为光入射到膜内的折射角。

## 次波源位置问题

两块玻璃夹空气层：因为波列断断续续，相干光光程差不能太大所以在玻璃表面的次波源无法发生干涉。

## 干涉条纹定域问题

- 点光源

> 理想点光源干涉全场可见，非定域干涉，但亮度小。

- 扩展光源

> 入射角不同或膜厚不均匀导致衬比度下降
>
> 某些面上会增强，称为定域中心，干涉称为定域干涉，定域中心前后一个小范围库看到条纹，称为定域深度。
>
> - 入射光平行
>
> > - 薄膜不平行
> >
> > > 干涉定域在膜附近，因为有一定深度，可以认为干涉定域在膜上。
> > >
> > > 干涉条纹和等厚线一致，称为等厚条纹。
> >
> > - 薄膜平行
> >
> > >干涉定域在无穷远处，可以使用凸透镜成像，构成等倾干涉。

# 等倾干涉

$\Delta=2nh\cos{r}+\frac{\lambda}{2}$

## 观察方法

点光源照射半反透镜，反射光入射到薄膜上，同心圆的入射角相等。通过透镜汇聚/人眼直接观察【干涉定域无穷远，需要眼睛放松】

## 级次分布

$k\uparrow r\downarrow$中心的级次最高

## 干涉条纹的间距

$r_{k+1}-r_{k}=-\frac{\lambda}{2nh\sin{r}}$

中心稀疏，边沿较密

## 膜厚改变时的移动规律

$h\uparrow \Delta- r\uparrow$厚度增大，干涉条纹从中心外冒。

从中心冒出一个$h$改变$\frac{\lambda}{2}$.

# 等厚干涉

通常垂直入射

$\Delta=2nh+\frac{\lambda}{2}$

## 观察方法

用半反透镜，使光垂直入射到薄膜上，干涉定域在薄膜附近，用眼睛看薄膜附近。用透镜时使薄膜成像在幕布上。

## 级次分布

- 劈尖状薄膜

> 从劈棱到底边级次依次升高。

- 牛顿环

> 从圆心到外侧级次依次升高。

## 干涉条纹的间距

- 劈尖状薄膜(劈角为$\alpha$)

> $d\Delta=\lambda$
>
> $dh=\frac{\lambda}{2n}$
>
> $l=\frac{\lambda}{2n\alpha}$

- 牛顿环

  > $\frac{r}{R}\approx 2\frac{h}{r}$
  >
  > $
  > r=
  > \left\{
  > \begin{aligned}
  > &\sqrt{(k+\frac{1}{2})R\lambda}\qquad,k=0,1,2,...,\qquad\mathrm{亮纹}\\
  > &\sqrt{(k+1)R\lambda}\qquad,k=-1,0,1,2,...,\qquad\mathrm{暗纹}
  > \end{aligned}
  > \right.
  > $
  >
  > $R=\frac{r^2_{k+m}-r^2_k}{m\lambda}$

## 干涉条纹的移动

- 劈尖状薄膜

> 厚度变大，向劈尖移动。

- 牛顿环

> 厚度增大，干涉条纹收缩到中心

## 应用

- 检验平整度
- 测量长度

> 看精确到半个波长的量级
>
> 可见光波长$10^2nm=10^{-5}cm$

- 增透膜

# 两种干涉仪

## 迈克耳孙干涉仪

$G_1$：分光板，使透射光强和反射光强差不多，保证衬比度较高。

$G_2$：补偿板，使两条支路的光通过玻璃板的次数均为三次，补偿通过玻璃板的光程差。

$M_2'$为$M_2$相对于$G_1$银镀层的虚像

- $M_2'$和$M_1$平行：等倾干涉
- $M_2'$和$M_1$不平行：等厚干涉

- 优点/地位

> - 使光路分开，可以插入其他装置研究
> - 原子发光波长稳定，推动了长度基准转向光波波长的自然基准
> - 迈克耳孙以此干涉仪测量地球相对以太的速度，为狭义相对论的建立准备了条件

## 马赫-曾德尔干涉仪

使两相干光束彻底分离，主要用于空气动力学和激波过程研究。

# 空间相干性

针对分波前干涉，描述光场在多大横向范围内两点引出的次波仍是相干的，又称为横向相干性。

$\frac{d}{R} < \theta_0=\frac{\lambda}{b}$时相干

用于天文测量，使用迈克尔逊星体干涉仪测量角直径。

- 迈克尔逊星体干涉仪

> $d$很大，干涉条纹很密集，需要大口径透镜。
>
> 使用4块平面镜缩小$d$

# 时间相干性

和单色性密切相关

波列长度$L_c=\frac{\lambda^2}{\Delta\lambda}$

# 多光束干涉

不考虑半波跃变

$\delta=\frac{2\pi}{\lambda}\Delta L=\frac{4\pi nh}{\lambda}\cos{\theta}$

反射多波束$
\left\{
\begin{aligned}
\tilde{U}_1&=rA=-r'A\\
\tilde{U}_2&=r'(tt')e^{i\delta}A\\
\tilde{U}_3&=r^{'3}(tt')e^{i2\delta}A\\
\tilde{U}_4&=r^{'5}(tt')e^{i3\delta}A\\
&\vdots
\end{aligned}
\right.
$

透射多波束$
\left\{
\begin{aligned}
\tilde{U}_1&=(tt')A\\
\tilde{U}_2&=r^{'2}(tt')e^{i\delta}A\\
\tilde{U}_3&=r^{'4}(tt')e^{i2\delta}A\\
\tilde{U}_4&=r^{'6}(tt')e^{i3\delta}A\\
&\vdots
\end{aligned}
\right.
$

总振幅和光强$
\left\{
\begin{aligned}
&\tilde{U}_T=\sum\limits_{i=1}^{\infty}\tilde{U}_i'=\frac{Att'}{1-r^2e^{i\delta}}=\frac{1-R}{1-Re^{i\delta}}A \\
&\tilde{U}_R=\sum\limits_{i=1}^{\infty}\tilde{U}_i \\
&I_T=\tilde{U}_T^*\tilde{U}_T=\frac{I_0(1-R)^2}{1+4R\sin^2{\frac{\delta}{2}}} \\
& I_R=\tilde{U}_R^*\tilde{U}_R=I_0-I_T=\frac{I_0}{1+\frac{(1-R)^2}{4R\sin^2(\frac{\delta}{2})}} \\
\end{aligned}
\right.
$

当$R\rightarrow 1,\frac{4R}{(1-R)^2}\gg1,I_T$对$\delta$变化敏感

反射率越高，衬比度越大

## 半值宽度

以$\frac{I_T}{I_0}$降到一半时两点的距离来定义条纹的宽度。

- 半值相位宽度

> $\varepsilon=\frac{2(1-R)}{\sqrt{R}}(rad)$

- 半值角宽度

> $\delta=\frac{2\pi}{\lambda}\Delta L=\frac{4\pi nh}{\lambda}\cos{\theta}$
>
> $\Delta\delta=-\frac{4\pi nh\sin{\theta}}{\lambda}\Delta\theta=\varepsilon$
>
> $|\Delta\theta_k|=\frac{\lambda}{2\pi nh\sin{\theta_k}}\frac{1-R}{\sqrt{R}}(rad)$

- 半值谱线密度（波长差）

> $\delta=\frac{2\pi}{\lambda}\Delta L=\frac{4\pi nh}{\lambda}\cos{\theta}$
>
> $\Delta\delta=-\frac{4\pi nh\cos{\theta}}{\lambda^2}\Delta\lambda_k=\varepsilon$
>
> $\Delta\lambda_k=\frac{\lambda^2}{2\pi nh\cos{\theta_k}}\frac{(1-R)}{\sqrt{R}}$
>
> $\Delta\upsilon_k=\frac{c\Delta\lambda_k}{\lambda^2}$

## 应用

- 法布里-珀罗干涉仪

> - 色散本领：同级不同波长干涉条纹分开的角度
>
> >$\Delta\theta_k=\frac{\lambda}{2\pi nh\sin{\theta_k}}\frac{1-R}{\sqrt{R}}(rad)$
> >
> >$\Delta\lambda_k=\frac{\lambda}{\pi k}\frac{1-R}{\sqrt{R}}$
>
> - 色分辨本领：条纹的细锐程度$A=\frac{\lambda}{\Delta\lambda}=k\frac{\pi\sqrt{R}}{1-R}$
>
> - 自由光谱范围：相邻光谱序之间不能重合

- 选频

> $2nh=k\lambda_k$
>
> $\upsilon_k=k\frac{c}{2nh}$光谱图上等间距分布

# 后记

一个平时没怎么看过课本，上课随便听听的人考了令人满意的成绩，此事也成为我搭建博客的推手。