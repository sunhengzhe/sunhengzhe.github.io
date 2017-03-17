---
title: 隐函数微分法
date: 2017-02-25 19:40:50
tags: [单变量微积分]
---

用隐函数微分法可以求导任意反函数，只要知道原函数的导数。

<!-- more -->

## 隐函数微分法

在 `求导四则运算和常见求导公式的推导` 一节里讨论了 $(x^n)'$ 在 n 为整数时的情况，现在试求 $y = x^{m/n}$(m、n 均为整数)的导数。

因为我们只知道当 n 为整数时 $x^n$ 的导数，所以给两边同时取 n 次方，得

$$
y^n = x^m
$$

实际上这就是把显函数转为了隐函数，两边同时微分，得

$$
\begin{align}
\cfrac{d}{dx}y^n &= \cfrac{d}{dx}x^m \\\
ny^{n - 1}y' &= mx^{m - 1} \\\
y' &= \cfrac{m}{n} \cdot \cfrac{x^{m - 1}}{y^{n - 1}} \\\
&= \cfrac{m}{n} \cdot \cfrac {x^{m - 1}}{(x^{\frac{m}{n}})^{n - 1}} \\\
&= \frac{m}{n} x^{m - 1 - \frac{m}{n}(n - 1)} \\\
&= \frac{m}{n} x^{\frac{m}{n} - 1}
\end{align}
$$

即 $(x^{\frac{m}{n}})' = \frac{m}{n} x^{\frac{m}{n} - 1}$，由此说明当 n 为分数时，$(x^n)' = nx^{n - 1}$ 同样适用。

## 反函数

设有 $y = \sqrt{x}, x > 0$

那么可得 $x = y^2$

设 $y = f(x) = \sqrt{x}$，$x = g(y) = y^2$

称 f 和 g 互为反函数，记 $g = f^{-1}$，$f = g^{-1}$

## 使用隐函数微分法求导反函数

### 1) $\arctan x$

试求 $y = \tan^{-1} x$ 的导数

因为 $\arctan$ 和 $\tan$ 互为反函数，两边取 $\tan$ 得

$$
\begin{align}
y &= \tan^{-1} x \\\
\tan y &= x \\\
\cfrac{d}{dx} \tan y &= \cfrac{d}{dx} x \\\
\cfrac{1}{\cos^2 y} \cdot y' &= 1 \\\
y' &= \cos^2 y
\end{align}
$$

根据 $\tan y = x$，我们可以构建如下直角三角形

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/implicit-differentiation/01.png)

那么，$\cos y = \cfrac{1}{\sqrt{1 + x^2}}$，$\cos^2 y = \cfrac{1}{1 + x^2}$

所以，$(\arctan x)' = \cfrac{1}{1 + x^2}$

### 2) $\arcsin x$

$$
\begin{align}
y &= \sin^{-1} x \\\
\sin y &= x \\\
\cfrac{d}{dx} \sin y &= \cfrac{d}{dx} x \\\
\cos y \cdot y' &= 1 \\\
y' &= \cfrac{1}{\cos y} \\\
&= \cfrac{1}{\sqrt{1 - \sin^2 y}} \\\
\end{align}
$$

而 $\sin y = x$，所以 $y' = \cfrac{1}{\sqrt{1 - x^2}}$

即 $(\arcsin x)' = \cfrac{1}{\sqrt{1 - x^2}}$
