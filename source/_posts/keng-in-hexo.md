---
title: HEXO 搭博客中的坑
date: 2016-08-27 09:10:57
tags: [每周总结]
---

怎么用 hexo 搭博客骑士官网已经写的很清楚了，这里只纪录一些问题和心得。

<!-- more -->

## 过程

写博客的时候，我们并不是在主分支，而是比如在 codes 分支，主分支上存放的是静态文件，也就是供用户访问的 html、css 等，这些都是 hexo 生成的。

当博客写完以后，过程如下：

```
hexo g
```

生成静态文件，此时所有静态文件存放在 `public` 目录下。

```
hexo s
```

搭建本地静态服务器，默认监听 4000 端口，此时可以通过 `localhost:4000` 访问你的网站，可以预览，看下有没有问题。

```
hexo d
```

部署博客，根据 `_config.yml` 部署到远程服务器上，如果使用的是 git，那么你应该部署到 master 分支上。

这样别人就能通过你的 `[username].github.io` 访问你的网站了，如果要关联你自己的域名，需要在 `source` 里添加 CNAME 文件，并使用

```
Plugins:
  - hexo-generator-cname
```

插件，注意大小写敏感。否则会报 `ERROR Deployer not found: git` 错误。

总结来看就是：

你在 codes 分支上写博客 -> `hexo g` -> `hexo d` -> hexo 将静态文件部署到远程 master 分支

## 404

如果你一不小心把 codes 分支覆盖到 master 上了，网站就报 404 了，根据上面的过程，很明显我们只需要让 hexo 重新部署一遍就可以了。

但是运行这两个命令之后并没有什么卵用，在执行这两个命令之前，你需要清空部署目录：

```
rm -rf .deploy_git/
```

然后继续 `hexo g`、`hexo d` 即可。