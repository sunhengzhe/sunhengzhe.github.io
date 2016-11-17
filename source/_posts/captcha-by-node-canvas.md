---
title: 使用 node-canvas 绘制验证码
date: 2016-11-17 21:59:14
tags: [node]
---

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/captcha-by-node-canvas/01.png)

最近要给 node 项目加上验证码，找到了 node-canvas 这个库，简单地用了一下，发现还是蛮好用的。

git 地址：[node-canvas](https://github.com/Automattic/node-canvas)

<!-- more -->

顾名思义，node-canvas 允许你在 node 端使用 canvas。在验证码这个使用场景里，我们可以先生成验证码，然后在 canvas 上绘制，最后将图片返回给前端。

## step 1 安装

在安装 node-canvas 之前，还需要安装一些依赖。不同的系统需要安装的不同，以 linux 和 mac 为例：

- linux:  `sudo yum install cairo cairo-devel cairomm-devel libjpeg-turbo-devel pango pango-devel pangomm pangomm-devel giflib-devel`
- mac: `brew install pkg-config cairo pango libpng jpeg giflib`

其他参考 [node-canvas#installation](https://github.com/Automattic/node-canvas#installation)

安装完依赖后，执行 `npm install canvas` 即可。

## step 2 画图

通过获取 canvas，可以得到 context 对象，然后就可以像在前端一样绘制图形了

```javascript
const Canvas = require('canvas');
const canvas = new Canvas(100, 30),
    ctx = canvas.getContext('2d');
```

实际上我用到的 api 和前端的 canvas 是一样的，绘制过程就不多解释，可以参考 canvas 的相关教程。

下面是绘制一个 `a + b = ?` 的验证码

```javascript
ctx.font = '24px "Microsoft YaHei"';

// 绘制文本
let drawText = (text, x) => {
  ctx.save();
  // 旋转角度
  const angle = Math.random() / 10;
  // y 坐标
  const y = 22;
  ctx.rotate(angle);
  ctx.fillText(text, x, y);
  ctx.restore();
}

// 随机画线
let drawLine = () => {
  const num = Math.floor(Math.random() * 2 + 3);
  // 随机画几条彩色线条
  for (let i = 0; i < num; i++) {
    const color = '#' + (Math.random() * 0xffffff << 0).toString(16);
    const y1 = Math.random() * height;
    const y2 = Math.random() * height;
  // 画线
    ctx.strokeStyle = color;
    ctx.beginPath();
    ctx.lineTo(0, y1);
    ctx.lineTo(width, y2);
    ctx.stroke();
  }
}

// 数字的文本随机从小写汉字、大写汉字、数字里选择
const numArr = [
  '〇一二三四五六七八九',
  '0123456789',
  '零壹贰叁肆伍陆柒捌玖'  
];
// 第一个数字
const fir = Math.floor(Math.random() * 10);
// 第二个数字
const sec = Math.floor(Math.random() * 10);
// 随机选取运算
const operArr = ['加', '减', '乘'];
const oper = Math.floor(Math.random() * operArr.length);

drawLine();
drawText(numArr[Math.floor(Math.random() * numArr.length)][fir], 10);
drawText(operArr[oper], 40);
drawText(numArr[Math.floor(Math.random() * numArr.length)][sec], 70);
drawText('=', 100);
drawText('?', 130);

// 验证码值的计算
let captcha;
switch(oper) {
  case 0: 
    captcha = fir + sec;
    break;
  case 1:
    captcha = fir - sec;
    break;
  case 2:
    captcha = fir * sec;
    break;
}

// 存入 session
req.session.captcha = captcha;
```

效果如下：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/captcha-by-node-canvas/01.png)

## step 3 返回图片

调用 `canvas.toDataURL()`，可以返回图片的 base64 格式数据。

```javascript
res.send({
  status: 200,
  data: canvas.toDataURL()
})
```

前端将该数据加到 img 的 src 属性中即可。

## 中文乱码

在将项目部署到 linux 后，发现输出显示的图片中的中文都变成了方框。

 我参考了 [redhat 6 下 node-canvas中文方框解决办法](https://my.oschina.net/u/129529/blog/266843) 这篇文章，但是没有全部运行，而是安装了 `yum groupinstall "Chinese Support"`，`yum groupinstall Fonts` 这两个。

另外参考 [用node-canvas绘制中文的时候乱码怎么解决？](https://cnodejs.org/topic/53f98ad8bbdaa79d518c0836) 问题里的 5 楼，使用了微软雅黑。

还有 [issue#461](https://github.com/Automattic/node-canvas/issues/461)，在字体两侧加上引号。

我按这三个做了，然后 **重启项目** 就好了~