---
title: Qt软件窗体添加阴影
date: 2017-10-26 18:47:43
categories: Qt
tags: 窗体阴影
---

> 软件加入影响效果，一方面让软件有立体感，另一方面也会提升逼格，那么如何给软件添加阴影效果呢？本文提供两种方案参考：

<!--more-->

### 一、 通过Windows API 重写Event事件添加
```
#include <QEvent>
#include <dwmapi.h>
#pragma comment(lib, "dwmapi.lib")
#pragma comment(lib, "user32.lib")
bool event(QEvent *event)
{
    static bool class_amended = false;
    if (event->type() == QEvent::WinIdChange)
    {
        HWND hwnd = reinterpret_cast<HWND>(winId());
        if (class_amended == false)
        {
            class_amended = true;
            DWORD class_style = ::GetClassLong(hwnd, GCL_STYLE);
            class_style &= ~CS_DROPSHADOW;
            ::SetClassLong(hwnd, GCL_STYLE, class_style);
        }
        DWMNCRENDERINGPOLICY val = DWMNCRP_ENABLED;
        ::DwmSetWindowAttribute(hwnd, DWMWA_NCRENDERING_POLICY, &val, sizeof(DWMNCRENDERINGPOLICY));

        // This will turn OFF the shadow
        // MARGINS m = {0};
        // This will turn ON the shadow
        MARGINS m = { 1 };
        ::DwmExtendFrameIntoClientArea(hwnd, &m);
    }
    return QWidget::event(event);
}
```
### 二、通过Qt自带的类QGraphicsDropShadowEffect实现
如果要通过这个类实现的话，窗体的周围要留出相应的margin，因为此种形式的阴影是与窗体一体的，不像第一种方法那样是包括在窗体周围的，如果不留出一定的margin是无法显示阴影的。
```
QGraphicsDropShadowEffect* effect = new QGraphicsDropShadowEffect;
effect->setBlurRadius(20);
effect->setColor(Qt::black);
effect->setOffset(0,0);
setGraphicsEffect(effect);
```