---
title: 幂级数
date: 2017-03-25 08:44:25
tags: [单变量微积分]
---

幂级数的形式为 $a\_0 + a\_1x + a\_2x^2 + \cdots$，记为 $\sum\_{n = 0}^{\infty}a\_nx^n$。

<!-- more -->

## 幂级数的收敛性

设 $S\_N = \sum\_{n = 0}^{N}a\_nx^n$，幂级数之和即 $S = \sum\_{n = 0}^{\infty}a\_nx^n = \lim\_{N to \infty}S\_N$，当极限存在时，幂级数收敛，极限不存在，幂级数发散。

若 $|x| < R$ 时，幂级数收敛，则称 R 为幂级数的 **收敛半径**。

### 几何级数

当 $a\_0 = a\_2 = a\_3 = \cdots = 1$ 时，幂级数 $1 + x + x^2 + x^3 + \cdots$ 可以看做公比为 x 的几何级数。

$$
\begin{align}
S &= 1 + x + x^2 + x^3 + \cdots \\\
S \cdot x &= x + x^2 + x^3 + x^4 + \cdots \\\
S - S \cdot x &= 1 \\\
S &= \cfrac{1}{1 - x}
\end{align}
$$

前提是 S 是存在的，如果 $x > 1$，S 趋近于无穷大，就得不到上式了，所以几何级数的收敛半径为 1.

## 泰勒公式

通过泰勒公式可以将函数转化为幂级数展开式。

泰勒公式的一般形式为

$$
f(x) = \sum\_{n = 0}^{\infty}\cfrac{f^{(n)}(0)}{n!}x^n
$$

一般地，$a\_n = \cfrac{f^{(n)}(0)}{n!}$

### 栗子

#### 1. $e^x$

因为 $f(x) = e^x$，则 $f^{(n)}(x) = e^x$，$f^{(n)}(0) = 1$

所以 $e^x = \sum\_{n = 0}^{\infty}\cfrac{1}{n!}x^n = 1 + x + \cfrac{x^2}{2!} + \cfrac{x^3}{3!} + \cdots$，收敛半径 $R = \infty$

当 $x = 1$ 时，有 $e = 1 + 1 + \cfrac{1}{2!} + \cfrac{1}{3!} + \cdots$

#### 2. $\frac{1}{1 + x}$

与几何级数非常类似，可求得

$1 - x + x^2 - x^3 + \cdots = \cfrac{1}{1 + x}$，收敛半径 $R = 1$

#### 3. $\sin x$

$f(x) = \sin x$，$f'(x) = \cos x$，$f''(x) = - \sin x$，依次类推，可求得 $a\_0 = 0$，$a\_1 = 1$，$a\_2 = 0$，$a\_3 = -1$，$a\_4 = 0$，每四项一循环。

所以 $\sin x = x - \cfrac{x^3}{3!} + \cfrac{x^5}{5!} - \cfrac{x^7}{7!} + \cdots$，收敛半径 $R = \infty$

## 幂级展开式的性质

### 乘积

$x\sin x = x^2 - \cfrac{x^4}{3!} + \cfrac{x^6}{5!} - cfrac{x^8}{7!} + \cdots$，收敛半径等于较小的那个，$R = \infty$

### 求导

因为 $\cos x = \sin' x$，而 $\sin x$ 的幂级展开式已经在上面求出。

则 $\cos x = 1 - \cfrac{x^2}{2!} + \cfrac{x^4}{4!} - \cfrac{x^6}{6!} + \cdots$，收敛半径不变，$R = \infty$

### 积分

$$
\begin{align}
\ln (1 + x) = \int\_0^x\cfrac{dt}{1 + t} &= \int\_0^x(1 - t + t^2 - t^3 + \cdots)dt \\\
&= \left[ t - \cfrac{t^2}{2} + \cfrac{t^3}{3} - \cfrac{t^4}{4} + \cdots \right]\_0^x \\\
&= x - \cfrac{x^2}{2} + \cfrac{x^3}{3} - \cfrac{x^4}{4} + \cdots
\end{align}
$$

收敛半径 $R = 1$。

### 变量替换

求 $e^{-t^2}$，令 $x = -t^2$

$$
\begin{align}
e^{-t^2} &= 1 + (-t^2) + \cfrac{(-t^2)^2}{2!} + \cfrac{(-t^2)^3}{3!} + \cdots \\\
&= 1 - t^2 + \cfrac{t^4}{2!} - \cfrac{t^6}{3!} + \cdots
\end{align}
$$
