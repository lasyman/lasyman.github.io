---
title: Qt多项目管理与编译
date: 2018-01-25 19:47:43
categories: Qt
tags: Qt子项目
---

> 用Qt进行项目开发的时候，尤其是大型项目，经常会涉及到多工程或者多模块，只要思想是将项目整体模块化，降低程序的复杂度和耦合性，使程序调试与维护更加简单。

<!--more-->

# 多模块Qt项目创建 #
### 创建多项目目录
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj1.jpg)
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj2.jpg)

### 创建子项目
* 如果程序是app程序是带界面的，则需要创建一个widgets application
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj3.jpg)
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj4.jpg)

* 创建library1 
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj5.jpg)
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj6.jpg)

* 选择模块
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj7.jpg)

* 总体结构如下
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj8.jpg)

* 配置总工程
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj9.jpg)

* 配置子项目
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/multiProj10.jpg)

至此一个多项目工程就建好了

### 命令行编译
有时候我们需要对程序的编译打包做自动化处理，那么就必须要用到命令行进行编译了，以mingw编译器为示例，如果是msvc的话可以直接用vs的命令编译解决方案即可。

新建一个batch脚本，编辑如下
```
@echo off
echo **************************************
echo *           编译脚本                  *
echo **************************************
echo.

echo **************************************
echo *            检查环境变量             *
echo **************************************
rem 设置环境变量
;此处设置Qt路径
set qtpath=D:\Qt\Qt5.4.1
if not exist %qtpath% (
	echo !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
	echo QT目录不存在请设置后再运行
	echo !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
	echo.
	pause
	exit
)

set path=%qtpath%\5.4\mingw491_32\bin;%qtpath%\Tools\mingw491_32\bin;%PATH%


echo **************************************
echo *            编译程序                 *
echo **************************************
rem 执行clean
del ..\bin\Release\*.a
del ..\bin\Release\*.exe
rem 执行qmake
qmake AppDemo_mingw5.4.pro -r -spec win32-g++ -o ../bin/Makefile.Release

rem 执行make
cd ..\bin
mingw32-make.exe -f Makefile.Release

echo 编译完成

pause
```

