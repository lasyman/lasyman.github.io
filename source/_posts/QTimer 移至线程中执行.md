---
title: QTimer 移至线程中执行
date: 2017-06-22 18:47:43
categories: Qt
tags: QTimer
---
> 程序中通常会用到定时器轮训做一些事情，有时候所做的事情比较复杂会影响所在线程的效率，那么我们就要考虑将定时器的执行内容移到另外的线程中执行。

<!--more-->
**本文介绍如何Qt下如何将定时器执行内容移至线程中执行**
```
QTimer *timer = new QTimer();
QThread *thread = new QThread();
timer->setInterval(1000);
timer->start();  //<1> 定时器要在线程启动前触发
timer->moveToThread(thread);
thread->start();
connect(timer, SIGNAL(timerout()), this, SLOT(onPorcessTimeout()), Qt::DirectConnection);//<2>信号连接方式要选择直连，不然不会在新的线程中
```

这样的话，定时器触发的槽函数就会在单独的线程中执行了，不会影响本来的线程