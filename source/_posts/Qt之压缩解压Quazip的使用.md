---
title: Qt使用Quazip进行压缩解压
date: 2018-02-09 09:02:11
categories: Qt
tags: Quazip
---


> Qt下进行压缩解压有两种方案： 1. 通过QProcess调用第三方工具，如:7zip等 2. 通过第三方库，如:Quazip
> 本文主要介绍Qt如何使用Quazip进行压缩解压操作

<!--more-->
## 环境
1. windows 10
2. Qt5.4 mingw版本 和Qt5.9 msvc版本

## 准备工作
1. 下载Quazip源码包, [Quazip 官网](http://quazip.sourceforge.net/)，本文使用的是Quazip0.7.3
2. 解压到工作目录

## 编译
1. 用Qt Creater打开quazip.pro，并修改如下

```
#去掉qztest相关
TEMPLATE=subdirs
SUBDIRS=quazip
```
![目录结构如下](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/quazip1.jpg)

2. 修改子项目quazip的工程文件quazip.pro，在末尾添加如下头文件路径(注意: QTDIR环境变量是Qt的目录，如:D:\Qt\Qt5.4.1\5.4\mingw491_32)
```
INCLUDEPATH += $$(QTDIR)/include/QtZlib
```
另外也可以拷贝$$(QTDIR)/include/QtZlib目录下的zconf.h和zlib.h到quazip目录下。
3. 设置对应的版本debug或者release，构建即可。

## 使用
1. 将对应动态库拷贝到你的项目中
2. 在工程中加入quazip的头文件，也可以直接拷贝到工程中
```
#include "quazip/JlCompress.h"
//压缩文件
JlCompress::compressFile(strZipFile, strFile);
//压缩目录
JlCompress::compressDir(strZipFile, strDirPath, bRecursive)
//解压
JlCompress::extractDir(strZipFile, strExtractPath);
```