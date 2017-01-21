---
title: hexo 中使用 mathjax 遇到的问题
date: 2017-01-21 21:30:45
tags: [总结]
---

这个博客里面用到的数学公式都是用 mathjax 写的，关于 mathjax 可以参考 [Mathjax与LaTex公式简介](http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/) 这篇文章。

这里记录使用 mathjax 过程中遇到的问题。

<!--more-->

### 与 markdown 冲突

#### 下划线 _

mathjax 里，使用下划线 _ 表示下标，但是在 markdown 里，下划线表示斜体的意思，所以在使用下标的时候，会碰到冲突的问题。

**解决办法**：在 `_` 前添加转义符：`\_`

### 公式对齐

在 mathjax 里，& 代表需要对齐的地方，在结尾处添加 `\\` 表示换行，但是在 markdown 中显示为了一整行：

$$
\begin{align}
\frac{\sum\_{i=1}^n(x\_i - \overline{X})^2}{n} & = \frac1n\sum\_{i=1}^n(x\_i - \overline{X})^2 \\
& = \frac1n\sum\_{i=1}^n[(x\_i - \mu) + (\mu - \overline{X})]^2 \\
\end{align}
$$

**解决办法**：将 `\\` 换成 `\\\`。
