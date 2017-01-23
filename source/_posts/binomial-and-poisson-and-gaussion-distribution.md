---
title: 二项分布、泊松分布、正态分布
date: 2017-01-22 22:28:45
tags: [统计学]
---

**二项分布、泊松分布和正态分布** 是统计学中十分重要的三个分布，也和我们的生活息息相关。

<!--more-->

## 二项分布

> 二项分布是 n 个独立的是/非试验中成功的次数的离散概率分布，其中每次试验的成功概率为 p

二项分布比较简单也比较常见，在此不作详细说明。

如给定一枚均匀的硬币，掷到正面的概率 p 和掷到反面的概率 1 - p 都是 0.5。设 X 为掷 n 次硬币中出现正面硬币的个数，那么
$$
P(X = k) = \binom{n}{k}p^k(1-p)^{n-k}
$$

其中，对于$k = 0, 1, 2, ... n$，$\binom{n}{k} = \frac{n!}{(n-k)!k!}$

### 期望

期望是指试验中每次可能结果的概率乘以其结果的总和，对于二项分布而言，期望 $E(X) = \sum_{k=0}^nk\binom{n}{k}p^k(1-p)^{n-k}$

而当 k = 0 时，$0 \times \binom{n}{0}p^0(1-p)^n = 0$，所以可以把第一项省略，于是

$$
\begin{align}
E(X) & = \sum\_{k=1}^nk\binom{n}{k}p^k(1-p)^{n-k} \\\
& = \sum\_{k=1}^nk\frac{n!}{k!(n-k)!}p^k(1-p)^{n-k} \\\
& = \sum\_{k=1}^n\frac{n!}{(k-1)!(n-k)!}pp^{k-1}(1-p)^{n-k} \\\
& = np\sum\_{k=1}^n\frac{(n-1)!}{(k-1)!(n-k)!}p^{k-1}(1-p)^{n-k} \\\
\end{align}
$$

令 a = k - 1，b = n - 1，则 n - k = b - a。从 k = 1 到 n 则可以替换为 a = 0 到  n - 1，即 a = 0 到 b

$$
\begin{align}
& = np\sum\_{a=0}^b\frac{b!}{a!(b-a)!}p^a(1-p)^{b-a} \\\
& = np \times 1 \\\
& = np
\end{align}
$$

即 $E(X) = np$

## 泊松分布

> 在二项分布中，如果试验次数 n 很大，二项分布的概率 p 很小，且乘积 $\lambda = np$ 比较适中，则事件出现的次数的概率可以用泊松分布来逼近。事实上，二项分布可以看作泊松分布在离散时间上的对应物。

假设你是一名交通工程师，你测出了每小时通过大桥的车辆为 $\lambda$ 辆，现在想知道每小时通过大桥的车是 k 辆的概率。

根据测量，实际上可以得出每分钟通过的车辆数，也就是 $\frac{\lambda}{60}$，事实上这可以看做 **每分钟有车通过的概率**

要想求出每小时通过大桥车辆数为 k 的概率 $P(X=k)$，利用上面讨论的二项分布，即有

$$
P(X=k) = \binom{60}{k}(\frac{\lambda}{60})^k(1 - \frac{\lambda}{60})^{60-k}
$$

这也有个问题，倘若每分钟有多辆车通过呢？一旦 k 超过 60，计算就有问题了，那么可以将 “每分钟” 换成 “每秒”，减少时间间隔。

$$
P(X=k) = \binom{3600}{k}(\frac{\lambda}{3600})^k(1 - \frac{\lambda}{3600})^{3600-k}
$$

当时间间隔越小，小到无限小的时候，可以引出泊松分布的形式：

$$
P(X=k) = \lim\_{n \to \infty}\binom{n}{k}(\frac{\lambda}{n})^k(1 - \frac{\lambda}{n})^{n-k}
$$

时间间隔无限小，也就是 n 趋近于正无穷。

$$
\begin{align}
P(X=k) & = \lim\_{n \to \infty}\binom{n}{k}(\frac{\lambda}{n})^k(1 - \frac{\lambda}{n})^{n-k} \\\
& = \lim\_{n \to \infty}\frac{n!}{k!(n-k)!}\frac{\lambda^k}{n^k}(1 - \frac{\lambda}{n})^{n}(1 - \frac{\lambda}{n})^{-k} \\\
& = \lim\_{n \to \infty}\frac{n(n-1)(n-2)\cdots(n-k+1)}{n^k}\frac{\lambda^k}{k!}(1 - \frac{\lambda}{n})^{n}(1 - \frac{\lambda}{n})^{-k}
\end{align}
$$


而 $\lim\_{n \to \infty}\frac{n(n-1)(n-2)\cdots(n-k+1)}{n^k} = 1，
\lim\_{n \to \infty}(1 - \frac{\lambda}{n})^n = e^{-\lambda}，
\lim\_{n \to \infty}(1 - \frac{\lambda}{n})^{-k} = 1$

所以，
$$
\begin{align}
P(X=k) & = 1 \times \frac{\lambda^k}{k!} e^{-\lambda} \times 1 \\\
& = \frac{\lambda^k}{k!} e^{-\lambda}
\end{align}
$$

## 正态分布

正态分布又名高斯分布，其概率密度函数为

$$
P(X) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{1}{2}(\frac{x-\mu}{\sigma})^2}
$$

其中 $\mu$ 为期望值，$\sigma$ 为标准差，之前也提到过。

另外，$\frac{x-\mu}{\sigma}$ 称为标准分数（z-score），代表 **距离平均数有多少个标准差**。

### 经验法则

经验法则又名 68-95-99.7 法则，表明了几个特殊区间的概率值。

$(\mu - \sigma, \mu + \sigma)$ 发生的概率为 68%
$(\mu - 2\sigma, \mu + 2\sigma)$ 发生的概率为 95%
$(\mu - 3\sigma, \mu + 3\sigma)$ 发生的概率为 99.7%

## 结语

需要注意的是二项分布和泊松分布都是离散分布，正态分布为连续分布。这三者存在紧密的联系，从图像上来看三者也是非常接近。

如果二项分布的实验次数 n 很大而每次试验的成功概率 p 很小时，泊松分布可作为二项分布的极限近似。当实验次数 n 再变大，几乎可以看成连续时，二项分布和泊松分布都可以用正态分布来代替。