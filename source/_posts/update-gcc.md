---
title: Run "sh -c node-gyp rebuild" error
date: 2016-10-26 11:43:34
tags: [总结]
---

npm 安装包的时候，出现 `Run "sh -c node-gyp rebuild" error` 异常，这是因为需要 C++ 11 的编译环境，使用 `gcc -v` 命令查看一下机器上 gcc 的版本，发现是 4.4.7，将其升级至 4.8.0 之后即可。

<!-- more -->

## 升级 gcc

### 1.下载源码包

```bash
wget http: //ftp.gnu.org/gnu/gcc/gcc-4.8.0/gcc-4.8.0.tar.bz2
tar -jxvf  gcc-4.8.0.tar.bz2
```

### 2.下载编译所需依赖库

```bash
cd gcc-4.8.0
./contrib/download_prerequisites
```


### 3.建立编译输出目录

```bash
cd ..
mkdir gcc-build-4.8.0
```

### 4.进入此目录，执行以下命令，生成makefile文件

```bash
cd  gcc-build-4.8.0
../gcc-4.8.0/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
```

### 5.编译

```bash
make -j4
```
*j 后面的是核心数，编译速度会比较快*

### 6.安装

```bash
sudo make install
```

## 问题
### 内存不足
![](http://7xo08n.com1.z0.glb.clouddn.com/blog/update-gcc/update-gcc-01.png)
出现这个问题是因为内存不足，释放内存之后重试一下