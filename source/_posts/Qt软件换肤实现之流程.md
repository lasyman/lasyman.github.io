---
title: Qt软件换肤实现之流程
date: 2017-06-23 18:47:43
categories: Qt
tags: 换肤
---

> 软件换肤大家应该都使用过，最常见的有一些在线音乐软件，安全管家之类。提供多种多样的皮肤选择也可以给软件增加不少友好度，那么对于Qt开发的软件如何实现这一功能呢？

> Qt提供qss来控制控件的样式，语法类似于css，但是并没有完全兼容css。不过已经足够实现换肤的功能了。

<!--more-->
**本文将介绍如何通过qss实现换肤**

**原理：**
- 不同的qss控制控件不同的样式
- 换肤时切换不同的qss资源

#### 一、新建资源qrc
- qss中会用到一些图标资源都是在qrc中
- 通过Qt Creator新建一个qrc资源，并设置好前缀
- 将要用到的图片资源添加进去

#### 二、新建qss文件
- 一般一个软件会包含多个页面或者窗体，如果把所有样式放到同一个qss里也是可以的，但是维护管理会需要花些时间。我们可以通过不同界面或者窗体一个qss文件。
- 一个窗体会用到很多相同的控件，比如button，不同的button会有不同的展示效果，qss需要根据button控件的objectname识别是哪个button，从而应用对应的样式。当然也可以根据自定义属性进行识别。
- 将qss也添加到qrc中
- qss示例（通过objectname识别）

```
QPushButton#btn1
{
	min-width: 300;//最小宽度 
	min-height: 30;//最小高度
    font: 12pt "微软雅黑";//字体
	background-image: url(:/button-image.png);//背景
	border: 1px solid #1a1a1a;//边
	qproperty-icon: url(:/icon.png);//图标
}
QPushButton#btn2
{
	min-width: 200; 
	min-height: 20;
    font: 12pt "微软雅黑";
	background-image: url(:/button-image1.png);
	border: 1px solid #1a1a1a;
	qproperty-icon: url(:/icon1.png);//图标
}
```
- qss示例（通过自定义属性识别）

```
// 代码设置自定义属性
QPushButton *btn1 = new QPushButton("btn1");
QPushButton *btn2 = new QPushButton("btn1");
btn1->setProperty("custome_property", "addBtn");
btn1->setProperty("custome_property", "delBtn");
// qss指定属性
QPushButton[custome_property="addBtn"]
{
	min-width: 300;//最小宽度 
	min-height: 30;//最小高度
    font: 12pt "微软雅黑";//字体
	background-image: url(:/button-image.png);//背景
	border: 1px solid #1a1a1a;//边
	qproperty-icon: url(:/icon.png);//图标
}
QPushButton[custome_property="delBtn"]
{
	min-width: 200; 
	min-height: 20;
    font: 12pt "微软雅黑";
	background-image: url(:/button-image1.png);
	border: 1px solid #1a1a1a;
	qproperty-icon: url(:/icon1.png);//图标
}
```
#### 三、生成皮肤文件
```
D:\Qt\Qt5.7.0\5.7\msvc2015\bin\rcc -binary xxx.qrc -o xxx.skin
```

#### 四、加载资源
所有界面的资源，一般在main函数内加载
```
#include <QResource>

QString skinFile = QString("xx/xxx.skin");//皮肤文件位置
QResource::unregisterResource(skinFile);
if (!QResource::registerResource(skinFile))
   throw "resource exception";
```

#### 五、应用样式
在对应界面的处理类中应用对应样式
```
void onChangeSkin()
{
	QFile file(":/qss/xx.qss");
	if (file.open(QFile::ReadOnly))
		this->setStyleSheet(file.readAll());
	file.close();
}
```

###总结：
多套皮肤通过对应控件触发，加载对应资源文件，然后发信号给界面类进行换肤处理。

