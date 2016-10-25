---
title: Express 权限控制
date: 2016-10-25 10:28:57
tags: [每周总结, express]
---

Express 是一个路由和中间件 Web 框架，其自身只具有最低程度的功能：Express 应用程序基本上是一系列中间件函数调用。

所谓的控制权限实际上是将资源和路由对应起来，当进入特定路由时，对用户进行权限判定。

<!-- more -->

项目结构参考 [[译\]Express应用结构的最佳实践](https://segmentfault.com/a/1190000004019233)

## 路由

首先路由的划分应该尽可能清晰：

```
├── controllers
│   ├── fr
│   │   └── index.js
│   ├── report
│   │   └── index.js
│   ├── user
│   │   └── index.js
│   └── index.js
└── app.js
```

index.js 是路由的入口文件，用来引用子路由以及一些没有常规前缀的路由，如首页路由。

### index.js

```javascript
const express = require('express');
const auth = require('../middlewares/auth');
const user = require('./user');
const fr = require('./fr');
const report = require('./report');
const router = express.Router();

router.use(auth);
router.use('/user', user);
router.use('/fr', fr);
router.use('/report', report);

module.exports = router;
```

当有请求进入匹配的路由时，Express 按路由定义的先后顺序加载中间件，这也就是说，当用户请求 `/user/get` 的时候，必定会加载 `/user` 这个路由（如果这两个路由都定义过）。所以，我们可以在根路由下设置中间件，在这个中间件里控制权限。**即上面代码中的 auth**

## 权限控制

auth 实际上就是一个中间件，只不过这个中间件是其他所有中间件的入口。

### auth.js

```javascript
'use strict';

const express = require('express');
const router = express.Router();
const user = require('../models/user');
const url = require('url');

const whiteAPI = ['/health', '/user/login', '/user/logout'];

module.exports = function(req, res, next) {

  let session = req.session;

  // 请求路由
  let route = url.parse(req.url).pathname;

  // 无须登录的接口
  if(whiteAPI.indexOf(route) !== -1) {
    next();
    return;
  }

  let username = session.username;

  // 判断是否登录
  if(!username) {
    // 未登录
    res.send({
      status: 401,
      message: '未登录'
    })
    return;
  }

  // 检查权限
  user.get(username).then(info => {
    let permission = info.permission;
    for(let p of permission) {
      let reg = new RegExp(p);
      if(reg.test(route)) {
        next();
        return;
      }
    }
    
    // 没有权限
    res.send({
      status: 403,
      message: '没有访问权限'
    })
  }).catch(err => {
    res.send({
      status: 500,
      message: err.message
    })
  });
}
```

如果没有权限或没有登录，直接返回错误状态，否则通过 `next()` 放行至下个路由。

## 用户状态保存

上面使用了 `session` 来保存用户信息，在express 中使用 session 可以引用 `express-session` 库。github 地址：[express-session](https://github.com/expressjs/session)

### app.js

```javascript
var session = require('express-session');
var RedisStore = require('connect-redis')(session);
var redis = require('redis');
var redisClient = redis.createClient({
  host: 'your redis host',
  port: 6379,
  password: 'your redis password'
});

// 路由
var router = require('./controllers');

var app = express();

// session
app.use(session({
  secret: 'your secret',
  // rolling: 每次请求都重新设置session cookie，即假设你的cookie是10分钟过期，每次请求都会再设置10分钟
  rolling: false,
  saveUninitialized: false,
  store: new RedisStore({
    client: redisClient
  }),
  cookie: {
    maxAge: 1 * 24 * 60 * 60 * 1000
  }
}));
```

express-session 默认是使用内存存储 session，所以一重启服务用户的状态就丢了，推荐使用 redis 或者 mongodb 来存储。上面例子使用了 redis。

## 注意

另外需要注意的是，如果前端是使用 ajax 进行请求，需要在 request 中带上 `withCredentials = true;`，并在服务端设置响应头 `Access-Control-Allow-Credentials: true`。

```javascript
res.setHeader('Access-Control-Allow-Origin', config.cors.origin);
res.setHeader('Access-Control-Allow-Credentials', true);
```

