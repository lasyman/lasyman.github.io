---
title: Qt 窗体置顶
date: 2018-03-23 09:05:06
categories: Qt
tags: windows
---

> 有时候当我们使用一款软件的同时也会做其他的一些事情，但是当我们触发其他鼠标或者键盘事件，此软件就可能会被我们使用的其他程序遮挡，那么我们就需要此软件具备置顶的效果，这样我们就可以一边做其他事情，又不耽误监控此软件。那么如何使窗体置顶呢？

<!--more-->

## 一、 利用Qt提供的窗体标签

```
Qt::WindowFlags flags = windowFlags();
flags |= Qt::WindowStaysOnTopHint;
setWindowFlags(flags);
```
我们对窗体加入此标签就可以使当前窗体保持最前了。

**注意： 这样虽然能将窗体置顶，但是有的程序仍然能够遮挡我们的程序，比如QQ等**

## 二、 利用WIndows API进行置顶操作
这种情况只能限于windows环境下

```
SetWindowPos((HWND)winId(),HWND_TOPMOST,pos().x(),pos().y(),width(),height(),SWP_SHOWWINDOW);
```

如果软件运行环境简单，窗体比较少的情况下Qt提供的方式即可。根据软件的使用环境选择置顶方式，