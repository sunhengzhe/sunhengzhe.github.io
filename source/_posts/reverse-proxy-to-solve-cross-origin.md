---
title: 反向代理解决跨域问题
date: 2017-05-13 20:58:45
tags: [nginx]
---

跨域问题是前端开发中经常会遇到的问题。

后端同学开发好了一些接口并部署到了服务器上，这时他提供给我们的地址是 `10.172.1.1`，而我们在本地开发时，一般都开的是 node 服务，比如 `localhost:3000`，此时使用 ajax 调用远程服务就会出现跨域问题。

解决跨域问题有很多种方法，比如 jsonp，但是我在工作中很少会碰到使用 jsonp 解决跨域问题的场景。

另外一种办法是在服务端设置跨域头 CORS，这是很常见的一种做法，也比较简单，但需要后端同学配合。本文主要探讨的是使用 nginx 配置反向代理来解决跨域问题。

### 配置

首先启动 node 服务，以监听 3000 端口为例。这时访问 `localhost:3000` 为前端页面。

#### 配置 nginx

```nginx
http {
    server {
        listen    2199;
        location / {
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://localhost:3000;
        }

        location ^~ /api/ {
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://10.172.1.1;
        }
    }
}
```

然后访问 `http://localhost:2199`，当后面为 `/api/..` 时，nginx 会转发给 `10.172.1.1`，而其他的非 api 接口，则转发给 node 服务。这样相当于从 `http://localhost:2199` 请求到 `http://localhost:2199/api/`，则解决了跨域问题。
