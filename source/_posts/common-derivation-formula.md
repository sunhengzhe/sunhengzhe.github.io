---
title: 求导四则运算和常见求导公式的推导
date: 2017-02-25 18:10:50
tags: [单变量微积分]
---

求导四则运算和常见求导公式的推导过程。

<!-- more -->

## 求导四则运算

### 乘法

设有函数 u、v

$$
\begin{align}
(uv)' &= \lim\_{\Delta x \to 0} \cfrac{\Delta (uv)}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{u(x + \Delta x) \cdot v(x + \Delta x) - u(x) \cdot v(x)}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{u(x + \Delta x) \cdot v(x + \Delta x) - u(x) \cdot v(x + \Delta x) + u(x) \cdot v(x + \Delta x) - u(x) \cdot v(x)}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{v(x + \Delta x) \cdot [u(x + \Delta x) - u(x)] + u(x) \cdot [v(x + \Delta x) - v(x)]}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{v(x + \Delta x) \cdot \Delta u + u(x) \cdot \Delta v}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{v(x) \cdot \Delta u + u(x) \cdot \Delta v}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \left( v\cfrac{\Delta u}{\Delta x} + u\cfrac{\Delta v}{\Delta x} \right) \\\
&= u'v + uv' \\\
\end{align}
$$

### 除法

#### 使用导数的定义

$$
\begin{align}
\Delta \cfrac{u}{v} &= \lim\_{\Delta x \to 0} \left( \cfrac{u + \Delta u}{v + \Delta v} - \cfrac{u}{v} \right) \\\
&= \lim\_{\Delta x \to 0} \cfrac{(u + \Delta u) \cdot v - u \cdot (v + \Delta v)}{(v + \Delta v) \cdot v} \\\
&= \lim\_{\Delta x \to 0} \cfrac{uv + \Delta u \cdot v - uv - u \cdot \Delta v}{(v + \Delta v)v} \\\
&= \lim\_{\Delta x \to 0} \cfrac{\Delta u \cdot v - u \cdot \Delta v}{(v + \Delta v) \cdot v}
\end{align}
$$

所以

$$
\begin{align}
(\cfrac{u}{v})' &= \lim\_{\Delta x \to 0} \cfrac{\Delta \cfrac{u}{v}}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{\cfrac{\Delta u}{\Delta x} \cdot v - u \cfrac{\Delta v}{\Delta x}}{(v + \Delta v) \cdot v} \\\
&= \cfrac{u'v - uv'}{v^2}
\end{align}
$$

#### 使用链式法则

由 $(x^a)' = ax^{a - 1}$ 得

$$
(\frac{1}{v})' = (v^{-1})' = -1 \cdot v^{-2}
$$

由此

$$
\begin{align}
(\frac{u}{v})' &= (uv^{-1})' \\\
&= u'v^{-1} + u(v^{-1})' \\\
&= u'v^{-1} + u(-1 \cdot v^{-2}) \\\
&= \cfrac{u'v - uv'}{v^2}
\end{align}
$$

## 常见求导公式

### 1) $\sin x$

$$
\begin{align}
\cfrac{d}{dx}\sin x &= \lim\_{\Delta x \to 0} \cfrac{\sin (x + \Delta x) - \sin x}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{\sin x\cos \Delta x + \cos x\sin \Delta x - \sin x}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \left( \sin x \cfrac{\cos \Delta x - 1}{\Delta x} + \cos x\cfrac{\sin \Delta x}{\Delta x} \right) \\\
\end{align}
$$

而 $\cfrac{\cos \Delta x - 1}{\Delta x}$ 实际上就是 $\cos x$ 在 x = 0 处的导数，即：

$$
\begin{align}
\lim\_{\Delta x \to 0} \cfrac{\cos \Delta x - 1}{\Delta x} &= \lim\_{\Delta x \to 0} \cfrac{\cos (0 + \Delta x) - \cos 0}{\Delta x} \\\
&= \left. \cfrac{d}{dx}\cos x \right |\_{x = 0} \\\
&= 0
\end{align}
$$

$\cfrac{\sin \Delta x}{\Delta x}$ 实际上就是 $\sin x$ 在 x = 0 处的导数，即：

$$
\begin{align}
\lim\_{\Delta x \to 0} \cfrac{\sin \Delta x}{\Delta x} &= \lim\_{\Delta x \to 0} \cfrac{\sin (0 + \Delta x) - \sin 0}{\Delta x} \\\
&= \left. \cfrac{d}{dx}\sin x \right |\_{x = 0} \\\
&= 1
\end{align}
$$

所以

$$
\begin{align}
\cfrac{d}{dx}\sin x &= \lim\_{\Delta x \to 0} \left( \sin x \cfrac{\cos \Delta x - 1}{\Delta x} + \cos x\cfrac{\sin \Delta x}{\Delta x} \right) \\\
&= \sin x \cdot 0 + \cos x \cdot 1 \\\
&= \cos x
\end{align}
$$

### 2) $\cos x$

$$
\begin{align}
\cfrac{d}{dx}\cos x &= \lim\_{\Delta x \to 0} \cfrac{\cos (x + \Delta x) - \cos x}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{\cos x \cos \Delta x - \sin x \sin \Delta x - \cos x}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \left( \cos x \cfrac{\cos \Delta x - 1}{\Delta x} - \sin x \cfrac{\sin \Delta x}{\Delta x} \right) \\\
&= \cos x \cdot 0 - \sin x \cdot 1 \\\
&= -\sin x
\end{align}
$$

所以，对于 $\cos x$ 和 $\sin x$ 而言，**只要知道了一个点 (x = 0) 处的导数，我们就知道了整个函数的导数。**

### 3) $\tan x$

$$
\begin{align}
\cfrac{d}{dx} \tan x &= \cfrac{d}{dx} \cfrac{\sin x}{\cos x} \\\
&= \cfrac{d}{dx} \cfrac{\sin' x\cos x + \sin x\cos' x}{\cos^2 x} \\\
&= \cfrac{\cos^2 x + \sin^2 x}{\cos^2 x} \\\
&= \cfrac{1}{\cos^2 x}
\end{align}
$$

### 4) $\sec x$

$$
\begin{align}
\cfrac{d}{dx} \sec x &= \cfrac{d}{dx} (\cos x)^{-1} \\\
&= -1 \cdot (\cos x)^{-2} (-\sin x) \\\
&= \cfrac{\sin x}{\cos^2 x} \\\
&= \cfrac{1}{\cos x} \cfrac{\sin x}{\cos x} \\\
&= \sec x \tan x
\end{align}
$$

### 5) $x^n$

#### 当 n 为正整数时

$$
\begin{align}
(x^n)' &= \lim\_{\Delta x \to 0} \cfrac{(x + \Delta x)^n - x^n}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{x^n  + nx^{n - 1}\Delta x + O((\Delta x)^2) - x^n}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \cfrac{nx^{n - 1}\Delta x + O((\Delta x)^2)}{\Delta x} \\\
&= \lim\_{\Delta x \to 0} \left( nx^{n - 1} + O(\Delta x) \right) \\\
&= nx^{n - 1}
\end{align}
$$

#### 当 n 为负整数时

先考虑当 n 为正整数时，试求 $\cfrac{1}{x^n}$ 的导数

$$
\begin{align}
(\cfrac{1}{x^n})' &= \cfrac{-nx^{n - 1}}{x^{2n}} \\\
&= -nx^{n - 1 - 2n} \\\
&= -nx^{-n - 1}
\end{align}
$$

即 $(x^{-n})' = -nx^{-n - 1}$，由此说明当 n 为负整数时，$(x^n)' = nx^{n - 1}$ 同样适用。

#### 其他情况

下一节讨论
