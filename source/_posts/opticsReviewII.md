---
title: 光学复习-II
date: 2024-01-16 19:16:18
tags:
    - 物理
    - 光学
excerpt: 光学期末考试复习
mathjax: true
---
# 衍射限制尺度、发散角、波长
$$
\rho\cdot\Delta\theta\sim\lambda
$$
$\rho\sim 10^3\lambda$以上，衍射效应不明显;

$\rho\sim 10^3--10\lambda$:衍射效应明显;

$\rho\le\lambda$:向散射过渡。

# 惠更斯-菲涅耳原理
$$
\tilde{U}(P)=K\oiint\limits_{(\Sigma)}\tilde{U}_0(Q)f(\theta_0,\theta)\frac{e^{ikr}}{r}\mathrm{d}S\\
f(\theta_0,\theta)=\frac{\cos{\theta_0}+\cos{\theta}}{2}\\
K=\frac{-i}{\lambda}=\frac{1}{\lambda}e^{-i\frac{\pi}{2}}
$$
$\Sigma$不限于等相位面，可以是任何隔离光源和场点的闭合曲面。

## 巴比涅原理
互补屏的叠加实际上是积分区域的叠加。

复振幅相反，光强相等。

# 菲涅耳衍射与菲涅耳半波带法
圆孔衍射中心时亮时暗，圆屏衍射中心总是亮斑。

![](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/半波带法分割波前.10erpf7j57cg.webp)
取一个球面波前,半径为$R$，以场点$P_0$为中心，以$b+\frac{k\lambda}{2}(k\in\mathbb{Z})$为半径把波前分割为环形带。
$$\tilde{U}(P_0)=\sum\Delta\tilde{U}_R=A_1-A_2+A_3-A_4+\cdots $$

$$\frac{\mathrm{d}\Sigma_k}{r_k}=\frac{\pi\lambda R}{R+b}=const $$

$f(\theta_0,\theta)$的下降速度非常缓慢。

![半波带法矢量叠加](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/半波带法矢量叠加.5a3r9lg54u0w.webp)

圆孔：$I_{max}(P_0)=4I_0$

圆屏：$I(P_0)\approx I_0$

## 菲涅耳波带片
只允许偶数/奇数的半波带透光，设有n个半波带。
$$
U(P_0)=\sum\limits_{i=0}^{n/2}A_{2i}\approx\frac{n}{2}A_1=nA_0 \\
I(P_0)\approx n^2I_0
$$

波带片半径公式：$\rho_k=\sqrt{k\frac{Rb\lambda}{R+b}}=\sqrt{k}\rho_1$

类似透镜公式：$\frac{1}{R}+\frac{1}{b}=k\frac{\lambda}{\rho^2} $

主焦点：$R\to\infty\qquad f_1=b=\frac{\rho_1^2}{\lambda} $

次焦点：$b$改变引起$k$改变。$f'=\frac{f_1}{3},\frac{f_1}{5},\cdots,\frac{f_1}{2m+1} $

波带片焦距随波长增加而缩短，和玻璃相反，两者配合可以用于消除色差。

# 夫琅禾费单缝衍射
缝分成窄条，首尾两窄条的相位差为$\delta$,衍射角为$\delta$
$$\delta=\frac{2\pi}{\lambda}a\sin{\theta} $$
$$
A=A_0\frac{\sin{\alpha}}{\alpha}\qquad(\alpha=\frac{\delta}{2})\\
I=I_0\frac{\sin^2{\alpha}}{\alpha^2}
$$

## 强度-衍射图样的特点
- 主极大：$\theta=0\quad I=I_0$
- 极小：$a\sin{\theta}=k\lambda$
- 次极大：$\alpha=\tan{\alpha}$ 
- 次极大强度：$4.7\%I_0,1.7\%,0.8\%\cdots $
- 半角宽度：$\sin{\theta_1}=\frac{\lambda}{a}\Rightarrow\Delta\theta_0\cdot a\approx\lambda $
- 主极大的宽度是次极大角宽度的两倍
- 越限制，越展宽
- 长波长半角宽度大。短波长峰值大

## 斜入射
宗量变化：$\alpha=\frac{\pi}{\lambda}a\sin{\theta}\Rightarrow\alpha=\frac{\pi}{\lambda}a(\sin{\theta}+\sin{i}) $

# 夫琅禾费方孔衍射，圆孔衍射，仪器的分辨本领
## 方孔
$$\alpha=\frac{\pi a}{\lambda}\sin{\theta_1}$$
$$\beta=\frac{\pi a}{\lambda}\sin{\theta_2}$$
$$I(p)=I_0\frac{\sin^2{\alpha}}{\alpha}\frac{\sin^2{\beta}}{\beta} $$
$$I_0=a^2b^2\frac{A^2}{\lambda^2f^2} $$

峰值零点半角宽度同单缝衍射。

## 圆孔
$$x=\frac{2\pi R\sin{\theta}}{\lambda} $$
$$I=I_0\left(\frac{2J_1(x)}{x} \right)^2$$
$$I_0=\frac{(\pi R^2)^2A^2}{\lambda^2f^2} $$
$$J_1(x)=\sum\limits_{K=0}^\infty(-1)^K\frac{1}{K!(1+K)!}(\frac{x}{2})^{2K+1} $$
|x|$0$|$1.22\pi$|$1.64\pi$|$2.23\pi$|$2.68\pi$|$3.24\pi$|
|-|-|-|-|-|-|-|
|$\frac{I}{I_0} $|$1$|$0$|$0.017$|$0$|$0.004$|$0$|

$84\%$的能量聚集在零级衍射斑（艾里斑）中。

艾里斑半角宽度：$1.22\frac{\lambda}{D},D$为直径

## 仪器的分辨本领
单透镜=两个透镜+光阑

夫琅禾费衍射在一切使用透镜的光学系统中存在。

瑞利判据：$\delta\theta=\Delta\theta_0$

# 多缝衍射与光栅
$$\alpha=\frac{\pi a}{\lambda}\sin{\theta}\qquad\beta=\frac{\delta}{2}=\frac{\pi d}{\lambda}\sin{\theta} $$
$$I(\theta)=I_0\left(\frac{\sin{\alpha}}{\alpha} \right)^2\left(\frac{\sin{N\beta}}{\sin{\beta}} \right)^2 $$
![多缝夫琅禾费衍射强度分布曲线](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/多缝夫琅禾费衍射强度分布曲线.lmro3wg8gr4.webp)
两个主峰间有$N-1$个零点，$N-2$个次极大值。次极强非常弱，衍射条纹为细锐的离散条纹。

- 极大值位置：$\beta=k\pi\qquad  d\sin{\theta_k}=k\lambda$
- 缺极：干涉极大和衍射极小重合。$d$和$a$成简单整数比。
- 半角宽度：$\frac{\lambda}{Nd} $,干涉条纹的$\frac{1}{N} $
- $N$变大，光的能量向主极大集中，单缝$N^2$倍，亮条纹更加细而亮。
- 量程问题：$d\sin{\theta_k}=k\lambda\quad\theta_k<\frac{\pi}{2} $

## 光谱光栅仪
- 角分辨率：$D_\theta=\frac{\delta\theta}{\delta\lambda}=\frac{k}{d\cos{\theta_k}} $
- 线色散率：$D_l=\frac{\delta l}{\delta\lambda}=fD_\theta $
- 色分辨率：$\delta\lambda_m=\frac{\lambda}{kN}\quad R=\frac{\lambda}{\delta\lambda}=kN $
- 自由光谱范围：第$k$极和第$k+1$极的光谱不重合的测量范围。

## 闪耀光栅
透射光栅的缺点：零极无色散，衍射光强分配到正负各能极。观察的那一级光谱只能分到极少的能量。

闪耀光栅的构造：反射式避免光栅吸收能量，把衍射和干涉零极错开。
![闪耀光栅的结构](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/闪耀光栅的结构.7azbwpnob85c.webp)
实际使用时主要采用沿N方向入射。
## 沿N方向入射
零极衍射角：$\theta=2\theta_b$

$$\alpha=\frac{\pi a}{\lambda}(\sin{(\theta-\theta_b)}-\sin{\theta_b}) $$
$$\beta=\frac{\pi d}{\lambda}\sin{\theta} $$

如果$\Delta L=d\sin(\theta)=\lambda $
则干涉一级主极大和衍射零极主极大重合。

由于$a\approx d$除一级主峰外，其他极都缺极，闪耀光栅只有一列光谱。

k极闪耀波长：$\Delta L=d\sin(\theta)=k\lambda_k\quad(k\in\mathbb{Z}) $

## 沿n方向入射
槽间光程差：$\Delta L=2d\sin{\theta_b}$

# X射线衍射
## 性质
产生方式：
- 高能电子打到靶上，电子受原子核电场作用减速，光谱连续
- 高能电子激发原子内层电子，光谱不连续

波长$10^{-11}-10^{-8}m $,穿透力强。

## 布喇格公式
$$2d\sin{\theta}=k\lambda$$
$\theta$为掠入射角（入射光线和晶面的夹角）

劳厄法看劳厄斑，德拜法看由亮环组成的德拜图。

# 全息术
波前的记录与再现。

记录：干涉

再现：衍射

# 光的偏振
圆偏振光：随时间统计矢量端点轨迹为圆

椭圆偏振光：随时间统计矢量端点轨迹为椭圆

迎着光线看，光矢量顺时针旋转为右旋。
![两个相互垂直的简谐运动合成](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/两个相互垂直的简谐运动合成.1bp88tg8696o.webp)
## 起偏器和检偏器
马吕斯定律（只适用于线偏振光）：$I_P(\alpha)=I_0\cos^2{\alpha} $

## 双折射
o光：遵从折射定律

e光：一般不遵从折射定律

光轴：某一方向不发生双折射

主截面：光轴和入射点法线组成的平面

主平面：光轴和光的传播方向构成的平面

o光的振动方向垂直于主平面，e光平行

入射面和主截面不一致时，e光折射光可能不在入射面内

主折射率：o光折射率为$n_o$，e光垂直光轴折射率为$n_e$

双折射率：$n_e-n_o$

正晶体：$n_e-n_o>0$，如石英（右图）

负晶体：$n_e-n_o<0$，如方解石（左图）
![单轴晶体的波面](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/单轴晶体的波面.2ozxzqdbq4n4.webp)

## 偏振棱镜
尼科耳棱镜：e光射出，o光全反射

格兰-汤姆森棱镜：尼科耳棱镜的改进型

洛匈棱镜、沃拉斯棱镜：分离两种光

## 波片
光轴平行于表面，o光和e光有附加相位差$\delta=\frac{2\pi}{\lambda}(n_o-n_e) $

$\delta>0$，o光速度慢，相位落后

- $\lambda$片：不改变偏振状态
- $\frac{\lambda}{2} $片：线偏振偏振方向关于光轴轴对称。圆偏振旋转方向反向
- $\frac{\lambda}{4} $片：圆偏振和线偏振相互转换（椭圆偏振主轴要和光轴平行）

区分五种偏振态：偏振片导致的光强变化和消光位置+$\frac{\lambda}{4} $片的转换

晶体补偿器：获得固定相位差

## 偏振光的干涉
两个垂直的偏振片相当于增加$\pi$的相位差

## 光弹测量
介质双折射率和应力成正比

## 电光效应
克尔效应：各向同性介质在外电场的作用下变为各向异性，产生双折射。

泡克斯效应：各向异性介质改变双折射率。

### 克尔效应
$$\Delta \varphi=\frac{2\pi}{\lambda}lbE^2=2\pi KlE^2$$
$K$为克尔常数。

应用:高速光电开关，电光调制器

缺点：所用液体常有剧毒，易爆炸

### 泡克斯效应
$$\Delta \varphi=\frac{2\pi}{\lambda}n_0^3\gamma El$$

## 旋光效应
转过的角度：
$$\varphi=\alpha d$$
$\alpha$为旋光率

### 法拉第光隔离器
两偏振片45度角摆放，中间的法拉第旋转器使光旋转45度，则一个方向光束通过，另一个到达第二个偏振片时偏振方向和偏振片方向垂直。

# 光的吸收
## 线性吸收规律
$$\mathrm{d}I=-\alpha I\mathrm{d}x $$
$\alpha$为吸收系数

线性介质,光强不太强时$\alpha$与光强无关。
- 比尔定律：溶液中$\alpha$和浓度成正比。当溶液浓度大到分子间相互作用影响分子吸收本领后偏离比尔定律。

## 普遍吸收和选择吸收
- 普遍吸收：吸收后所有成分的光强改变。
- 选择吸收：只强烈吸收某些波长的光。
> 原子吸收光谱
>
> 大气窗口：水，二氧化碳 光学通信常用波段1.55微米

## 复折射率
实部：折射率$n$决定介质中光速。

虚部：吸收系数$\alpha$决定介质中光强的衰减。
$$\tilde{E}(x,t)=A_0e^{(ik-\frac{\alpha}{2})x}e^{-i\omega t}=A_0e^{i\tilde{k}x}e^{-i\omega t} $$
$$\tilde{k}=k+i\frac{\alpha}{2}=\frac{\omega}{c}\tilde{n} $$
$$\tilde{n}=n+i\frac{c\alpha}{2\omega}=n(1+i\kappa) $$

# 光的色散
## 正常色散
对光波透明的介质，折射率随波长增加减小，色散率$\frac{\mathrm{d}n}{\mathrm{d}\lambda}<0 $

柯西公式：$n=A+\frac{B}{\lambda^2}+\frac{C}{\lambda^4}$

波长范围不太大时只需要取前两项。

## 反常色散
在介质对光有强烈吸收的波段内，折射率随波长的增加而增大，色散率$\frac{\mathrm{d}n}{\mathrm{d}\lambda}>0 $

## 全域色散曲线
![一种介质的全域色散曲线](https://cdn.jsdelivr.net/gh/RedrockerLi/RedrockerLi.github.io@main/Pics/一种介质的全域色散曲线.4ita7ia49s74.webp)
- 存在一系列吸收带
- 两相邻吸收带之间，$n$随$\lambda$的增加单调变小
- 每经过一个吸收带，$n$显著增大。曲线总趋势随$\lambda$上升而抬高
- $\lambda=0,n=1$
- 极短波$n<1$
