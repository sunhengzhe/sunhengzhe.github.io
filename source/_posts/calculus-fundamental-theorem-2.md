---
title: 微积分第二基本定理
date: 2017-03-12 16:46:20
tags: [单变量微积分]
---

> 如果 f 连续，且 $G(x) = \int\_a^xf(t)dt$，那么 $G'(x) = f(x)$。$(a \leqslant t \leqslant x)$

<!-- more -->

另一种表述形式即对 f 积分再求导，会得到函数本身：$\cfrac{d}{dx}\int\_a^xf(t)dt = f(x)$

微积分第二基本定理的意义在于 G(x) 可以用来求解微分方程 y' = f，初始条件为 y(a) = 0。

## 几何意义

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/calculus-fundamental-theorem-2/1.png)

如图，$G(x)$ 为 $f(x)$ 在 a 到 x 之间的积分，设 $\Delta G$ 为 f(x) 在 x 到 $\Delta x$ 之间的积分。

则 $\Delta G \approx \Delta x \cdot f(x)$，即 $\lim\_{\Delta x \to 0} \cfrac{\Delta G}{\Delta x} = f(x)$

## 举例

### 例一

求 $\cfrac{d}{dx}\int\_1^x \cfrac{dt}{t^2}$

即求 $\cfrac{d}{dx}G(x) = G'(x) = f(x) = \cfrac{1}{x^2}$

### 例二

设 $L(x) = \int\_1^x \cfrac{dt}{t}$，证 $L(ab) = L(a) + L(b)$

$$
\begin{align}
L(ab) &= \int\_1^{ab}\cfrac{dt}{t} \\\
&= \int\_1^{a}\cfrac{dt}{t} + \int\_a^{ab}\cfrac{dt}{t} \\\
&= L(a) + \int\_a^{ab}\cfrac{dt}{t}
\end{align}
$$

即证 $\int\_a^{ab}\cfrac{dt}{t} = L(b)$

令 $t = au$，则 $dt = adu$，代入上式

$\int\_a^{ab}\cfrac{dt}{t} = \int\_1^{b}\cfrac{adu}{au} = \int\_1^{b}\cfrac{du}{u} = L(b)$

另外，根据微积分第二基本定理，可以快速得到 $L(1) = 0$，$L'(x) = \cfrac{1}{x}$，则 $L'(1) = 1$，$L''(x) = -\cfrac{1}{x^2}$

可以画出 L(x) 图像的趋势，其实即对数函数

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/calculus-fundamental-theorem-2/2.png)

### 例三

$F(x) = \int\_0^x e^{-t^2}dt$

根据微积分第二基本定理，$F(0) = 0$，$F'(x) = e^{-x^2}$，所以 $F'(0) = 1$，$F''(x) = -2xe^{-x^2}$

$$
F''(x)
\begin{cases}
\> 0,  & \text{$x < 0$} \\\
< 0, & \text{x > 0}  \\\
\end{cases}
$$

可得 $F(x)$ 的函数趋势为一个奇函数，而 $F'(x)$ 的函数图像为

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/calculus-fundamental-theorem-2/3.png)

是概率论中常见的钟形函数。
