---
title: QGraphicsView下使用setCursor的坑
date: 2018-9-13 11:41:29
categories: Qt
tags: QGraphicsView
---

> 在Qt图形框架下编程，通常做编辑操作时会用到根据选择的不同图形改变鼠标样式起到美观、人性化交互的效果。 通常的拖拽、改变大小之类都可以通过默认样式进行设置，特殊的样式就需要用图片进行设置。

<!--more-->

### 一、设置鼠标样式的方式

1. **使用默认样式**
在QT界面的根类QWidget中有以下
```
QCursor cursor(); 
void setCursor(QCursor &); 
void unsetCursor();
```
三个方法，因而对于一切直接或间接继承自QWidget的控件都可以设置鼠标进入控件范围的鼠标形状。
默认鼠标样式如下：
![](https://blog-res.oss-cn-hongkong.aliyuncs.com/pics/mouse_cursor.png)

2. 使用图片
方法如下：
```
QCursor(const QBitmap &bitmap, const QBitmap &mask, int hotX = -1, int hotY = -1);
QCursor(const QPixmap &pixmap, int hotX = -1, int hotY = -1);
setCursor(QPixmap("*.png"));
```

### 二、 问题及解决办法
我的画布是继承QGraphicsView的，当选择不同图形需要设置不同的鼠标样式，通过setCursor()设置，但是鼠标样式有时生效有时却突然没有了，虽然setCursor()正常执行了。 
1. 使用viewport()->setCursor()就可以了
2. 同理类似于用viewport()->update()代替update()

