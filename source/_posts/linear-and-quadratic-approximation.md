---
title: 线性近似和二阶近似
date: 2017-03-12 09:45:00
tags: [单变量微积分]
---

线性近似和二阶近似是导数的重要应用之一，通过近似能够简化函数。一个合理的近似常常可以帮助解决问题。

<!-- more -->

## 线性近似

线性近似也就是一阶近似，通过直线来拟合函数。如在 $f(x) = \sin x$ 中，当 $x$ 无限接近 0 时，此时 $\sin x$ 的图像近似于 $\sin x$ 在 $x = 0$ 处的切线，即:

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/linear-and-quadratic-approximation/1.png)

根据导数的定义

$$
\begin{align}
\lim\_{\Delta x \to 0}\frac{\Delta f}{\Delta x} &= f'(x\_0) \\\
\frac{\Delta f}{\Delta x} &\approx f'(x\_0) \\\
\Delta f &\approx f'(x\_0) \cdot \Delta x \\\
f(x) - f(x\_0) &\approx f'(x\_0) \cdot (x - x\_0) \\\
f(x) &\approx f(x\_0) + f'(x\_0) \cdot (x - x\_0)
\end{align}
$$

所以，线性近似的公式为:

$$
当 x \approx x\_0 时，f(x) \approx f(x\_0) + f'(x\_0)(x - x\_0)
$$

### 常见函数的线性近似

**当 $x \approx 1$ 时，**

$$
\ln x \approx \ln 1 + \cfrac{1}{1}(x - 1) = x - 1
$$

**当 $x \approx 0$ 时，**

$$
\begin{align}
\sin x &\approx \sin 0 + \cos 0 \cdot x = x \\\
\cos x &\approx \cos 0 - \sin 0 \cdot x = 1 \\\
e^x &\approx e^0 + e^0 \cdot x = x + 1 \\\
\ln (1 + x) &\approx \ln (1 + 0) + \cfrac{1}{1 + 0} \cdot x = x \\\
(1 + x)^r &\approx (1 + 0)^r + r \cdot (1 + 0)^{r - 1} \cdot x = rx + 1
\end{align}
$$

需要注意的是，因为线性近似是使用直线拟合，所以求线性近似时，需要在得到的结果中需要舍弃高次项（大于 1 次的项）。

### 栗子

求 $\cfrac{e^{-3x}}{\sqrt{1 + x}}$ 在 $x \approx 0$ 时的线性近似。

$$
\begin{align}
\cfrac{e^{-3x}}{\sqrt{1 + x}} &= e^{-3x} \cdot (1 + x)^{\cfrac{1}{2}} \\\
&\approx (1 - 3x)(\frac{1}{2}x + 1) \\\
&= 1 - \frac{7}{2}x + \frac{3}{2} x^2 \\\
&\approx 1 - \frac{7}{2}x
\end{align}
$$

## 二阶近似

二阶近似比线性近似更精确一点，在线性近似精确度不够的时候使用

$$
f(x) \approx f(x\_0) + f'(x\_0)(x - x\_0) + \cfrac{f''(x\_0)}{2}(x - x\_0)^2
$$

可以看出，二阶近似实际上是一阶近似 + $\cfrac{f''(x\_0)}{2}(x - x\_0)^2$

### 为什么二阶导数要除 2？

对于抛物线来讲，二阶近似就是它本身

设有抛物线 $f(x) = a + bx + cx^2$，那么它的线性近似函数也为 $f(x) = a + bx + cx^2$。

而 $f(0) = a$，$f'(0) = b$，$f''(0) = 2c$

所以 $a = f(0)$，$b = f'(0)$，$c = \cfrac{f''(0)}{2}$

### 常见函数的二阶近似

**当 $x \approx 0$ 时，**

$$
\begin{align}
\sin x &\approx x + \cfrac{-\sin 0}{2}x^2 = x \\\
\cos x &\approx 1 + \cfrac{-\cos 0}{2}x^2 = 1 - \cfrac{1}{2}x^2 \\\
e^x &\approx x + 1 + \cfrac{e^0}{2}x^2 = 1 + x + \cfrac{1}{2}x^2 \\\
\ln (1 + x) &\approx x + \cfrac{-(1 + 0)^{-2}}{2}x^2 = x - \cfrac{1}{2}x^2 \\\
(1 + x)^r &\approx 1 + rx + \cfrac{r(r - 1)(1 + 0)^{r - 2}}{2}x^2 = 1 + rx + \cfrac{r(r - 1)}{2}x^2
\end{align}
$$

同样地，求二阶近似时，需要在得到的结果中需要舍弃大于 2 次的项。

### 几何意义

对高阶函数来说，二阶近似相比一阶近似更逼近原函数

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/linear-and-quadratic-approximation/2.png)
