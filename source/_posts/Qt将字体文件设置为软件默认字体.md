---
title: Qt将字体文件设置为软件默认字体
date: 2017-06-23 18:47:43
categories: Qt
tags: QFontDatabase
---

> 有时候软件使用的字体可能操作系统并不包含，那么我们就需要附加字体文件，那么Qt下如何使用字体文件并设置为软件默认字体呢？

<!--more-->

# 将字体文件设置为软件字体

### - 引入头文件
```
#include <QFontDataBase>
```
- 将字体文件路径传给addApplicationFont,得到字体的ID
- 将字体Id传给applicationFontFamilies,得到一个QStringList,其中的第一个元素为新添加字体的family
- 将此字体设为QApplication的默认字体

### -示例：
```
int fontId = QFontDatabase::addApplicationFont("./res/msyh.ttf");
QString msyh = QFontDatabase::applicationFontFamilies ( fontId ).at(0);
QFont font(msyh,10);//字体大小
QApplication::setFont(font);
```