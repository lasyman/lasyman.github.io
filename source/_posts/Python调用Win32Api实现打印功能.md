---
title: Python调用Win32Api实现打印功能
date: 2018-03-21 15:20:05
categories: Python
tags: 打印
---

> 最近使用python调用windows 驱动操作打印机，可是发现暂时并没有这样的第三方库可以使用，没办法只能通过win32Api实现了，好在python已经有了win32Api的库了，而且也很完善了，那么这样就简单多了。

<!--more-->

## 一、下载安装pywin32的库并安装
1. 通过pip进行安装

```
pip install pywin32
```
2. 下载对应安装包安装
[https://github.com/mhammond/pywin32](https://github.com/mhammond/pywin32)

## 二、 win32 Api的相关说明

[http://timgolden.me.uk/pywin32-docs/win32_modules.html](http://timgolden.me.uk/pywin32-docs/win32_modules.html)
![](https://blog-res.oss-cn-hongkong.aliyuncs.com/pics/win32Api%E6%96%87%E6%A1%A3.png)

根据需要选择对应模块，里面有相关的方法和简要说明，本文使用的是win32print


下面文档中有不同场景的打印示例，如打印文字的使用，打印图片的使用方法等。

[用Python在windows系统中打印.pdf](http://blog-res.oss-cn-hongkong.aliyuncs.com/resource/%E7%94%A8Python%E5%9C%A8windows%E7%B3%BB%E7%BB%9F%E4%B8%AD%E6%89%93%E5%8D%B0.pdf)