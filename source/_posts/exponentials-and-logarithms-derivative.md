---
title: 指数与对数函数导数
date: 2017-02-25 23:03:50
tags: [单变量微积分]
---

使用 e 底法和对数微分法求解指数与对数函数导数。

<!-- more -->

## $a^x$

试求 $a^x$ 的导数。

$$
\begin{align}
\cfrac{d}{dx} a^x &= \lim\_{\Delta x \to 0} \cfrac{a^{x + \Delta x} - a^x}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} a^x \cfrac{a^{\Delta x} - 1}{\Delta x}
\end{align}
$$

即 $\cfrac{d}{dx} = a^x \lim\_{\Delta x \to 0} \cfrac{a^{\Delta x} - 1}{\Delta x}$

而 $\lim\_{\Delta x \to 0} \cfrac{a^{\Delta x} - 1}{\Delta x}$ 实际上就是 $a^x$ 在 x = 0 处的导数，只要我们知道了在这个点处的导数，我们就能得到整个函数的导数，这和求 $\sin$、$\cos$ 的导数时如出一辙，但是很不幸的，我们并不知道这个点出的导数。

### 引入神秘的 e

设 $\lim\_{\Delta x \to 0} \cfrac{a^{\Delta x} - 1}{\Delta x}$ 为关于 a 的函数 $M(a)$，不妨设存在一个数 e，使得 $M(e) = 1$

那么，$\cfrac{d}{d} e^x = e^x \cdot M(e) = e^x$，且 $\left. \cfrac{d}{d} e^x \right|\_{x = 0} = 1$

注意，虽然我们到现在还不知道 e 是什么数，但我们可以先利用 e 得到一些公式，随后再求解 e。

## 自然对数

以 e 为底数的对数叫做自然对数，记为 $\ln$，且

$$
\begin{align}
\ln (a + b) &= \ln a + \ln b \\\
\ln a^n &= n\ln a
\end{align}
$$

### $\ln x$ 的导数

$$
\begin{align}
w &= \ln x \\\
e^w &= x \\\
e^w \cdot w' &= 1 \\\
w' &= \frac{1}{e^w} \\\
&= \frac{1}{x}
\end{align}
$$

即 $(\ln x)' = \cfrac{1}{x}$

## 求解 $a^x$ 的导数

### e底法

将 $a^x$ 转换为以 e 为底的指数。

因为 $a = e^{ln a}$，代入得

$$
\begin{align}
\cfrac{d}{dx}a^x &= \cfrac{d}{dx}(e^{\ln a})^x \\\
&= \cfrac{d}{dx} e^{x\ln a} \\\
&= \ln a \cdot e^{x\ln a} \\\
&= \ln a \cdot a^x
\end{align}
$$

> 而 $\cfrac{d}{dx} a^x = a^x \cdot M(a)$，所以，$M(a) = ln a$

### 对数微分法

对某些函数而言，求其对数的导数比求该函数本身的导数更简单。

设有函数 u，现要求其导数 $\cfrac{d}{dx} u$，那么

$$
\begin{align}
\cfrac{d}{dx} \ln u &= \cfrac{d}{du} \ln u \cdot \cfrac{d}{dx} u \\\
&= \cfrac{u'}{u}
\end{align}
$$

对 $a^x$ 而言，设 $u = a^x$，由对数的性质，$\ln u = \ln a^x = x\ln a$，那么 $(\ln u)' = \ln a$

而 $\cfrac{d}{dx} \ln u = \cfrac{u'}{u}$，所以即

$$
\begin{align}
\cfrac{u'}{u} &= \ln a \\\
u' &= u \ln a \\\
&= a^x \ln a
\end{align}
$$

## 几个栗子

### 1) $x^a$

之前已经讨论过求导 $x^a$，a 为有理数情况，实际上求导公式对无理数同样适用

#### e 底法

$$
\begin{align}
x^a &= (e^{\ln x})^a \\\
&= e^{a \ln x} \\\
(e^{a \ln x})' &= e^{a \ln x} \cdot \frac{a}{x} \\\
&= x^a \cdot \frac{a}{x} \\\
&= ax^{a - 1}
\end{align}
$$

#### 对数微分法

$$
\begin{align}
u &= x^a \\\
\ln u &= a \ln x \\\
(\ln u)' &= \frac{a}{x} \\\
\frac{u'}{u} &= \frac{a}{x} \\\
u' &= u \frac{a}{x} \\\
&= x^a \frac{a}{x} \\\
&= ax^{a - 1}
\end{align}
$$

### 2) $x^x$

求 $x^x$ 的导数

$$
\begin{align}
v &= x^x \\\
\ln v &= x \ln x \\\
\cfrac{v'}{v} &= \ln x + x \cdot \cfrac{1}{x} \\\
&= \ln x + 1 \\\
v' &= v \cdot (\ln x + 1) \\\
&= x^x(\ln x + 1)
\end{align}
$$

### 3) 例 3

求 $\lim\_{n \to \infty} (1 + \frac{1}{n})^n$

对函数取对数，得 $\ln (1 + \frac{1}{n})^n = n \ln(1 + \frac{1}{n})$

设 $\Delta x = \frac{1}{n}$，因为 $n \to \infty$，所以 $\Delta x \to 0$

$$
\begin{align}
\lim\_{n \to \infty} n \ln(1 + \frac{1}{n}) &= \lim\_{\Delta x \to 0} \frac{1}{\Delta x} \ln (1 + \Delta x) \\\
&= \lim\_{\Delta x \to 0} \cfrac{\ln(1 + \Delta x) - \ln 1}{\Delta x} \\\
\end{align}
$$

即 $\ln x$ 在 x = 1 处的导数，而 $\left. (\ln x)' \right|\_{x = 1} = \left. \frac{1}{x} \right|\_{x = 1} = 1$，即 $\lim\_{\Delta x \to 0} \ln (1 + \frac{1}{n})^n = 1$，而

$$
\begin{align}
\lim\_{n \to \infty} (1 + \frac{1}{n})^n &= \lim\_{n \to \infty} e^{\ln (1 + \frac{1}{n})^n} \\\
&= e^{\lim\_{n \to \infty} \ln (1 + \frac{1}{n})^n} \\\
&= e
\end{align}
$$

这也就是说，神秘的 $e = \lim\_{n \to \infty}(1 + \frac{1}{n})^n$
