---
title: node-canvas 中的中文乱码问题
date: 2016-11-18 19:20:14
tags: [node]
---

因为有小伙伴说使用 node-canvas 中文乱码，使用上一篇的方法也没能解决，于是我又上网找了一下，发现了这篇文章：[Nodejs图片编辑和中文乱码](http://www.jianshu.com/p/a651258c9135)

虽然原作者是使用的 gm，但他提到了一种解决方案：

<!-- more -->

> 我们来分析下解决乱码的两个条件：
>
> 1. 文档utf-8编码：这里utf-8编码是指要求文件的存储编码方式是utf-8，而不是对字符进行utf-8编码（如js中的encodeURI）。通过对IDE或者其他方式确定文件的编码方式，如有问题可自行百度。
> 2. 指定中文字体库：可以通过 `.font('/Library/Fonts/微软雅黑.ttf') //mac系统下` 或 `.font('/usr/share/fonts/微软雅黑.ttf')  //linux系统下`指定。 font函数的参数是字体在系统中的存储路径。
>
> 文／狐尼克朱迪（简书作者）
> 原文链接：http://www.jianshu.com/p/a651258c9135
> 著作权归作者所有，转载请联系作者获得授权，并标注“简书作者”。

也就是自己下载字体包，然后自行引用。扔一个下载地址：[字体下载](http://ztxz.org/)

于是我马上去 node-canvas 找了一下，非常开心地发现 node-canvas 也提供了这样的方法：[Canvas.registerFont for bundled fonts](https://github.com/Automattic/node-canvas/tree/master/.github#canvasregisterfont-for-bundled-fonts)，使用方法也是大同小异，设置字体路径，然后定义字体即可：

```javascript
// You need to call it before the Canvas is created
Canvas.registerFont('comicsans.ttf', {family: 'Comic Sans'});

var canvas = new Canvas(500, 500),
  ctx = canvas.getContext('2d');

ctx.font = '12px "Comic Sans"';
ctx.fillText(250, 10, 'Everyone hates this font :(');
```

结果一刷新网页发现竟然报了 `Canvas.registerFont is not a function`。一脸懵逼，明明 README 里面是这么写的啊！

又去查了一下，找到了这个 issue：[issue#838](https://github.com/Automattic/node-canvas/issues/838)，大兄弟的问题和我一模一样。

按照他们说的，这个方法在 npm 上还没有，但是 github 这个分支是有的：[https://github.com/chearon/node-canvas#12971f64a66b](https://github.com/chearon/node-canvas#12971f64a66b)

于是我把这个分支 clone 下来，然后把 `require('canvas')` 改成了 `require('../node-canvas')`

运行！Perfect！

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/node-canvas-chinese/01.png)

泪流满面