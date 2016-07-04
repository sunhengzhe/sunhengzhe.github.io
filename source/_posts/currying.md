---
title: 函数柯里化
date: 2016-07-04 12:00:28
tags: [javascript]
---

> 在计算机科学中，柯里化（英语：Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。   ———— 维基百科

## 什么是柯里化

先举个栗子：

这里有个简单的求和方法 `add`

```javascript
function add(a, b) {
  return a + b;
}
```

调用它：

```javascript
add(1, 2);  // 3
add(3, 3);  // 6
```

接下来我们使用柯里化的思想。

> 柯里化声称 “如果你固定某些参数，你将得到接受余下参数的一个函数”。

也就是说，如果满足柯里化思想，应该是这样的：
```javascript
function add(a, b) {
  return a + b;
}

var curryingAdd = currying(add, 5);

curryingAdd(1); // 6
curryingAdd(2); // 7
```

本来应该接收两个参数的 `add`，通过 `currying` 加工之后，可以用来接收余下的参数。

上面的例子里面，其实就是把 `add` 接收的参数固定了一个，也就是 5，我们可以想到 `curryingAdd` 的一种简单实现。

```javascript
var curryingAdd = function(a) {
  return function(b) {
    return a + b;
  }
}

curryingAdd(5)(1);  // 6
```

原理即是通过闭包保存了第一个变量。

## currying 方法

那么将普通函数转变为柯里化函数的 `currying` 是如何实现的呢，通过 `curringAdd`，可以想到使用闭包来实现。

```javascript
function currying(func) {
  // 闭包保存固定参数
  var firstArgs = Array.prototype.slice.call(arguments, 1);

  return function() {
    // 将固定参数和新参数一起传入原函数
    var args = firstArgs.concat(Array.prototype.slice.call(arguments));
    return func.apply(null, args);
  }
}

function add(a, b) {
  return a + b;
}

currying(add, 5)(1);  // 6
```

这种实现有一定的局限性，`curring` 方法的返回函数只能被执行一次，也就是我们不能这样调用：`currying(add)(1)(2)`

要满足灵活的调用，需要判断接收的参数个数：

```javascript
function currying(func) {
  // 闭包保存固定参数
  var firstArgs = Array.prototype.slice.call(arguments, 1);

  // 原函数接收的参数个数
  var l = func.length;

  return function inner() {
    // 将固定参数和新参数一起传入原函数
    firstArgs = firstArgs.concat(Array.prototype.slice.call(arguments));
    if(firstArgs.length < l) {
      return inner;
    }
    return func.apply(null, firstArgs);
  }
}

function add(a, b) {
  return a + b;
}

currying(add, 1)(5);  // 6
currying(add)(1)(5);  // 6
currying(add)(1, 5);  // 6
```

不过，我们还得考虑到原函数不用接收参数的情况，比如下面的 `sum`：
```javascript
function sum() {
  var s = 0;
  for (var i = 0, len = arguments.length; i < len; i++) {
    s += arguments[i];
  }
  return s;
}
```

`sum` 可以接收任意个参数并返回它们的和，我们通过调整 `currying` 的判断条件来覆盖这种情况。

```javascript
function currying(func) {
  // 闭包保存固定参数
  var firstArgs = Array.prototype.slice.call(arguments, 1);

  // 原函数接收的参数个数
  var l = func.length;

  return function inner() {
    // 将固定参数和新参数一起传入原函数
    var newArgs = Array.prototype.slice.call(arguments);
    firstArgs = firstArgs.concat(newArgs);

    if((l == 0 && newArgs.length == 0) || (l > 0 && firstArgs.length == l)) {
      // 调用完成
      return func.apply(null, firstArgs);
    }

    return inner;
  }
}

currying(sum, 1)(5)();  // 6
currying(sum)(1)(5)(6)(7)();  // 19
currying(sum)(1)(5)(6, 7)();  // 19

currying(add, 1)(5);  // 6
currying(add)(1)(5);  // 6
currying(add)(1, 5);  // 6
```