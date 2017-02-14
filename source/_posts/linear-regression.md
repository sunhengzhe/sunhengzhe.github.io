---
title: 线性回归
date: 2017-02-14 21:40:45
tags: [统计学]
---

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/linear-regression/1.png)

上图坐标轴中随机散落了一些点，现在想求出一条直线，使得这些点到这条直线的距离是最小的，如下图。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/linear-regression/2.png)

设这条直线的方程为 $y = mx + b$，即要找出 m, b，让上面蓝色的箭头之和最小。那么，所有点到直线的距离之和为：

$$
\begin{align}
SE &= (y\_1 - (mx\_1 + b))^2 + (y\_2 - (mx\_2 + b))^2 + ... + (y\_n - (mx_n + b))^2 \\\
&= (y\_1^2 - 2y\_1(mx\_1 + b) + (mx\_1 + b)^2) + (y\_2^2 - 2y\_2(mx\_2 + b) + (mx\_2 + b)^2) \\\
&+ ... + (y\_n^2 - 2y\_n(mx\_n + b) + (mx\_n + b)^2) \\\
&= (y\_1^2 - 2mx\_1y\_1 - 2by\_1 + m^2x\_1^2 + 2mbx\_1 + b^2) + (y\_2^2 - 2mx\_2y\_2 - 2by\_2 + m^2x\_2^2 + 2mbx\_2 + b^2) \\\
&+ ... + (y\_n^2 - 2mx\_ny\_n - 2by\_n + m^2x\_n^2 + 2mbx\_n + b^2) \\\
&= (y\_1^2 + y\_2^2 + ... + y\_n^2) - 2m(x\_1y\_1 + x\_2y\_2 + ... + x\_ny\_n^2) - 2b(y\_1 + y\_2 + ... + y\_n) \\\
&+ m^2(x\_1^2 + x\_2^2 + ... + x\_n^2) + 2mb(x\_1 + x\_2 + ... + x\_n) + nb^2 \\\
\end{align}
$$

设 $x\_1$、$x\_2$、...、$x\_n$ 的平均数为 $\overline{x}$，同样，可以计算出 $\overline{y}$，$\overline{xy}$，$\overline{x^2}$，$\overline{y^2}$，将平均数带入，即

$$
\begin{align}
SE &= (y\_1^2 + y\_2^2 + ... + y\_n^2) - 2m(x\_1y\_1 + x\_2y\_2 + ... + x_ny\_n) - 2b(y\_1 + y\_2 + ... + y\_n) \\\
&+ m^2(x\_1^2 + x\_2^2 + ... + x\_n^2) + 2mb(x\_1 + x\_2 + ... + x\_n) + nb^2 \\\
&= (n\overline{y^2}) - 2m(n\overline{xy}) - 2b(n\overline{y}) + m^2(n\overline{x^2}) + 2mb(n\overline{x}) + nb^2 \\\
\end{align}
$$

因为各项平均数是可以求出来的，所以这是一个关于 m、b 的方程式，实际上，这是一个碗状的图像，要求出让 SE 最小的 m、n，即求出这个图像的最低点，此时，对 m 和 b 分别求偏导得到的结果为 0，即

$$
\begin{align}
\frac{\partial{SE}}{\partial{m}} = -2n\overline{xy} + 2mn\overline{x^2} + 2bn\overline{x} &= 0 \\\
\frac{\partial{SE}}{\partial{b}} = -2n\overline{y} + 2mn\overline{x} + 2nb &= 0 \\\
\end{align}
$$

化简得

$$
\begin{align}
\overline{x^2}m + \overline{x}b - \overline{xy} &= 0 \\\
\overline{x}m - \overline{y} + b &= 0 \\\
\end{align}
$$

可以得到，该直线必定经过 $(\overline{x}, \overline{y})$ 和 $(\frac{\overline{x^2}}{\overline{x}}, \frac{\overline{xy}}{\overline{y}})$

当然，这条直线的方程也就能得到了：

$$
\begin{align}
m &= \frac{\overline{y} - \frac{\overline{xy}}{\overline{x}}}{\overline{x} - \frac{\overline{x^2}}{\overline{x}}} \\\
&= \frac{\overline{x} * \overline{y} - \overline{xy}}{(\overline{x})^2 - \overline{x^2}} \\\
b &= \overline{y} - m\overline{x}
\end{align}
$$

举个栗子。

还是上面那个例子，这些点的坐标如下：

```
[10.0, 8.04],
[8.0, 6.95],
[13.0, 7.58],
[9.0, 8.81],
[11.0, 8.33],
[14.0, 9.96],
[6.0, 7.24],
[4.0, 4.26],
[12.0, 10.84],
[7.0, 4.82],
[5.0, 5.68]
```

具体计算过程就不列了。。列个计算代码

```javascript
var data = [
    [10.0, 8.04],
    [8.0, 6.95],
    [13.0, 7.58],
    [9.0, 8.81],
    [11.0, 8.33],
    [14.0, 9.96],
    [6.0, 7.24],
    [4.0, 4.26],
    [12.0, 10.84],
    [7.0, 4.82],
    [5.0, 5.68]
];

let sumX = 0, sumY = 0, sumX2 = 0, sumY2 = 0, sumXY = 0;

for (let i = 0; i < data.length; i++) {
    const x = data[i][0], y = data[i][1];

    sumX += x;
    sumY += y;
    sumX2 += x * x;
    sumXY += x * y;
}

const averX = sumX / data.length;
const averY = sumY / data.length;
const averX2 = sumX2 / data.length;
const averXY = sumXY / data.length;

const m = (averY - averXY / averX) / (averX - averX2 / averX);
const b = averY - m * averX;

// 0.5, 3
console.log(m, b);
```

对应直线即

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/linear-regression/3.png)
