---
title: Qt信号阻塞与断开使用
date: 2018-9-18 12:14:34
categories: Qt
tags: Qt
---

> 有时候我们的程序处于某个状态时需要做轮询操作，但是当离开此状态或者不在此状态的窗口时，终止轮询操作可以减少一定的资源浪费，也可以降低程序会被轮询卡死的情况。
> Qt程序一般会用定时器进行超时信号槽的连接，那么在特定时间用信号的阻塞与断开，来终止轮询就可以起到很好的效果。

<!--more-->

### 一、 信号的阻塞
阻塞的方法：
```
bool QObject::blockSignals(bool block);
//If block is true, signals emitted by this object are 
//blocked (i.e., emitting a signal will not invoke 
//anything connected to it). If block is false, no such 
//blocking will occur.

//The return value is the previous value of signalsBlocked
().
//Note that the destroyed() signal will be emitted even if 
//the signals for this object have been blocked.
//Signals emitted while being blocked are not buffered.
```
使用例子：
```
void enableCheck(bool bCheck)
{
	if (bCheck) {
		this->blockSignals(false);
		//! DO SOMETHING
	} else {
		this->blcokSignals(true);
		//! DO SOMETHING
	}
}
```

### 信号断开
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/signal_disconnect.png)

用信号断开可以选择要断开的信号

使用例子：
```
void enableCheck(bool bCheck)
{
	if (bCheck) {
		connect(sender, signal, recever, slot);
		//! DO SOMETHING
	} else {
		disconnect(sender, signal, recever, slot);
		//! DO SOMETHING
	}
}
```