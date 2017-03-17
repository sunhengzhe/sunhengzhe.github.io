---
title: Javascript 中的线程与进程
date: 2016-08-27 07:43:34
tags: [总结, javascript]
---

写 javascript 写久了，就会对 javascript 的异步写法习以为常，反而不会好奇为什么在 javascript 里可以异步执行一个任务，是因为 javascript 是多线程吗？

实际上，javascript 从来都是单线程的。目前 javascript 有两个主要的运行环境，一个是浏览器，一个是 nodejs，下面分别从这两个方面对这个问题进行解释。

<!-- more -->

## 浏览器

javascript 的设计初衷，是为了解决一些简单的网页互动，也就是操作 DOM 元素。而如果采用多线程，那么大家都知道，多线程是可能会造成竞争资源的，很可能多个线程同时操作一个 DOM 元素，造成不必要的麻烦，所以 javascript 从诞生开始就选择了单线程执行。

然而在前端开发中，会经常用到 `setTimeout` 这个函数，我们可以规定让一段逻辑在指定时间后执行：

```javascript
setTimeout(function() {
  console.log('timeout done');
}, 3000);

console.log('main task')
```

上面这段代码会先输出 `main task`，然后在 3 秒之后输出 `timeout done`。就好像 setTimeout 开启了一个新线程一样，实际上并不是这样。

### Event loop

实际上， JavaScript 运行时包含了一个待处理的消息队列，每一个消息都与一个函数相关联。同时有一个事件循环不停地在轮询这个队列，当队列不为空时，就从队列中取出一个消息进行处理。它的实现大概如此：

```javascript
while(queue.waitForMessage()){
  queue.processNextMessage();
}
```

所以，上面的例子调用 [setTimeout](https://developer.mozilla.org/en-US/docs/window.setTimeout) 函数会在一个时间段过去后在队列中添加一个消息，实际上没有创建一个新的线程，当时间到达时，如果在队列中它的前面还有其他消息没有处理，那么它也要先等前面的执行完才执行。所谓的异步执行，实际上是在主线程空闲的时候执行，可以看如下代码：

```javascript
var startTime = new Date();

// 第一个异步任务
setTimeout(function() {
    console.log('task 1 start.');
    while(true) {
        var curTime = new Date();
        if(curTime - startTime > 5000) {
            break;
        }
    }
    console.log('task 1 done.')
}, 100);

// 第二个异步任务
setTimeout(function() {
    console.log('task 2 start.');
}, 500);

console.log('main');
```

程序第一个输出 `main`，这个毋庸置疑，但是第一个异步任务会执行 5 秒，而我们设置了第二个异步任务在 500 毫秒后执行，如果是多线程，`task 2 start` 会在 `task 1 done` 之前输出，然而结果是等 `task 1 done` 输出后才会输出 `task 2 done`。因为到 500 ms 时，task 2 被插入到消息队列中，然而此时task 1 还没有执行完，所以 task 2 紧接着 task 1 执行。示意图如下：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/thread-in-javascript/01.png)

### webworker

但是 javascript 能不能支持多线程呢，答案是可以的，在 HTML 5 规范中，新增了webworker 用来进行计算量大的操作，避免阻塞主线程，不过在webworker 创建的线程中，是不能操作 DOM 元素的，当 webworker 中的计算完成时可以通过事件与主线程进行通信。

## Nodejs

说完了浏览器端，现在说回 nodejs，nodejs 只是 javascript 的一个运行环境，实际上并不改变 javascript 是单线程执行的本质，但是 nodejs 可以提供一些方式来进行多线程和多进程开发。

### 多线程

#### [tagg](https://github.com/xk/node-threads-a-gogo)

Jorge Chamorro Bieling是 `tagg(Threads a gogo for Node.js)` 包的作者，他硬是利用 `phread` 库和C语言让Node.js支持了多线程的开发

### libuvd

`libuv` 是一个跨平台的异步I/O库，它主要用于Node.js的开发，同时他也被 `Mozilla's Rust language`,  `Luvit`, `Julia`,  `pyuv`等使用。它主要包括了 `Event loops` 事件循环， `Filesystem` 文件系统， `Networking` 网络支持，`Threads` 线程，`Processes` 进程，`Utilities` 其他工具。



### 多进程

#### child_process

可以启动一个 nodejs 文件，将它作为 worker 进程，实现多进程解决主进程阻塞的问题。

#### cluster

`cluster`可以用来让Node.js充分利用多核cpu的性能，同时也可以让Node.js程序更加健壮，官网上的`cluster`示例已经告诉我们如何重新启动一个因为异常而奔溃的子进程。



## 参考

1. [Node.js的线程和进程](https://github.com/DoubleSpout/threadAndPackage/blob/master/chapter.7.thread_and_process.md)
2. [Node.js 探秘：初识单线程的 Node.js](http://taobaofed.org/blog/2015/10/29/deep-into-node-1/)
3. [细说JavaScript单线程的一些事](https://segmentfault.com/a/1190000004276027)
4. [并发模型与Event Loop](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoo)