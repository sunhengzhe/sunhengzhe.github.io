---
title: 使用 svg 让线条动起来
date: 2016-06-28 20:43:19
tags: [每周总结, svg]
---
[百度echarts3官网](http://echarts.baidu.com/index.html) 上有一个非常好看的绘制 logo 的效果，通过 svg 就能实现：
![绘制logo](http://7xo08n.com1.z0.glb.clouddn.com/blog%2Fuse-svg-draw-line/02.gif)

与此类似的还有绘制签名的过程：
![绘制签名](http://7xo08n.com1.z0.glb.clouddn.com/blog/use-svg-draw-line/01.gif)

其实原理很简单，只要充分发挥想象力，加上好看的设计，就能完成很多意想不到的效果了。

<!-- more -->

## 准备 svg
首先要准备绘制的路径，SVG里用 path 来定义路径
```html
<svg width="580" height="400" xmlns="http://www.w3.org/2000/svg">
  <path d="m149,123c0,1 -1.496231,3.907784 -2,8c-0.122177,0.992508 -2.206802,4.812653 -4,7c-2.285873,2.78833 -3.49295,5.234009 -6,10" id="svg_27" fill-opacity="null" stroke-opacity="null" stroke-width="1.5" stroke="#000" fill="none"/>
</svg>
```
网上可以找到很多在线的或客户端可以用来生成 svg，比如写这个 demo 用到的是 [这个在线编辑svg的网站](http://www.yyyweb.com/ctools/demo.php)。

## 主要属性
有了一段 path 之后，我们就可以开始让它动起来了，这里涉及到两个属性：
- stroke-dasharray
- stroke-dashoffset

`stroke-dasharray` 可以让线条变成虚线，第一个值设置每段虚线的长度，第二个值设置虚线之间的间隔，如：

原线条：
![](http://7xo08n.com1.z0.glb.clouddn.com/blog/use-svg-draw-line/03.png)

设置 `stroke-dasharray: 20 5` 之后：
![](http://7xo08n.com1.z0.glb.clouddn.com/blog/use-svg-draw-line/04.png)

`stroke-dashoffset` 用来设置虚线的偏移量

## 原理
可以想到，如果我们设置每段虚线的长度和虚线之间的间隔都大于整条路径的长度，那么虽然看起来没有变化，但实际上整个路径显示出来的只是一小段虚线。

接着我们设置 `stroke-dashoffset` 让这条虚线偏移一个路径的长度，此时路径就不可见了，因为已经虚线已经偏移出路径了，而虚线间的空隙是大于一个路径长的。

最后我们通过动画让 `stroke-dashoffset` 缓慢变为 0，这时就有绘制的效果了。

代码示例：
```javascript
var path = document.querySelector('path');
// 获取路径的长度
var length = path.getTotalLength();
// 设置起始点
path.style.strokeDasharray = `${length} ${length}`;
path.style.strokeDashoffset = length;
// 获取一个区域，获取相关的样式，让浏览器寻找一个起始点。
path.getBoundingClientRect();
// 定义动作
path.style.transition = 'stroke-dashoffset 2s ease-in-out';
// 开始绘制
path.style.strokeDashoffset = '0';
```

当要绘制签名这种有很多线条的图形时，只需要给每一段都设置好时间，通过 `transition` 的 `delay` 来控制开始时间即可。

当要绘制本文一开始出现的闭合图形时，可以通过改变 `stroke-dasharray` 中的长度和间隔来控制。
