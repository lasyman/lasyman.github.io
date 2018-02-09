---
title: pyside使用
date: 2017-10-26 19:47:43
categories: python
tags: pyside
---

> PySide是Qt界面框架对Python语言的绑定，可以用Python语言和Qt进行界面开发。

<!--more-->

#### 1 安装Pyside
- 通过Pip安装
```
pip install PySide
```
如果没有出错表示安装成功了，测试PySide的版本
![](http://otfqaruit.bkt.clouddn.com/pyside_1.png)

- 下载Pyside的安装包
[下载路径](http://mirrors.ustc.edu.cn/qtproject/official_releases/pyside)

#### 2 使用Pyside
- 通过QtDesigner设计一个简单的登陆界面，并保存为login.ui
![](http://otfqaruit.bkt.clouddn.com/pyside_2.png)
- 通过PySide-uic命令生成对应的python文件
```
pyside-uic login.ui -o ui_login.py
```
- 使用Qt界面文件ui_login.py
![](http://otfqaruit.bkt.clouddn.com/pyside_3.png)

这样就可以调用Qt界面了，控件属性跟Qt使用基本一样

#### 3 使用Qt资源文件qrc
```
pyside-rcc login.qrc-o login_qrc.py
```
将此资源文件导入到ui_login.py
```
import login_qrc
```
**qrc一般记录界面要用的图片资源**

#### 4 使用qss进行样式设置
添加界面qss样式文件，内容如下：
```
*
{
	font-family: "微软雅黑";
	font: 13px;
}
QWidget#Form
{
	background: #323232;
}
QLineEdit
{
	background-color: #484849;
	border: 1px solid #646464;
	color: #c8c8c8;
}
QLabel
{
	color: #159BE3;
}
QLabel#lblPic
{
	background-image: url(:/pic/wellcom.jpg);
}
QPushButton
{
	width: 75px;
	height: 27px;
	background-color: #15A0E8;
	border-radius: 5px;
	color: white;
}
QPushButton:hover
{
	background-color: #177CE8;
}
QPushButton:pressed
{
	background-color: #1790E8;
}
```
在ui_login.py 添加如下方法
```
def setStyle(self, Form):
        file = codecs.open("login.qss", 'r', 'utf-8')
        style = file.read()
        Form.setStyleSheet(style)
        file.close()
```

在main.py初始化中添加
```
self.ui.setStyle(self)
```
最后效果：
![](http://otfqaruit.bkt.clouddn.com/pyside_4.png)

[测试源码](http://ougipk3bp.bkt.clouddn.com/pyside_test.zip)

