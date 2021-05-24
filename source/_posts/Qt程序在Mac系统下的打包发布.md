---
title: Mac 下Qt程序的打包发布（DMG版本）
date: 2019-11-12 10:47:43
categories: Qt
tags: Qt
---



> 平时都是在Windows下开发Qt程序，对于打包发布有各种方法，也是比较简单的。最近需要把程序在Mac下编译发布一版，网上的各种教程都是零零碎碎的，而且有的教程也是版本比较老的了，对于我用的Mac OS 10.15不太适用，经过一番google和尝试终于成功了，在此记录一下。

<!--more-->

## 一、程序打包

使用QtCreator编译成release版本之后，会生成一个.app的目录，这个目录就是Mac下的可执行文件，叫做bundle包。

- 1. 无第三方库

  如果程序没有第三方链接库的话，打包还是比较简单的，使用Qt自带的命令就可以完成

  ```shell
  # macdeloyqt appname.app -dmg
  ```

  执行完命令后，就会在对应目录下生成一个dmg的磁盘文件，此时程序交给其他人就可以正常打开运行了。当然这个dmg文件比较简陋，没有像我们下载其他程序那样好看，后面会讲如何美化我们的程序包。

- 2. 有第三方库

  如果程序使用了第三方库，那么通过上述命令打包的执行文件是无法运行的，会Library not loaded的错误。

  如：我一个程序叫MyApp，引用了一个动态连结库叫MyLib，将动态链接库拷贝到对应目录，完成编辑。

  ![mac1.png](http://nas.masterxx.top:10088/images/2021/05/23/mac1.png)

  双击MyApp，会报如下错误：

  ![mac2.png](http://nas.masterxx.top:10088/images/2021/05/23/mac2.png)

  

  如果程序依赖多个第三方库，可以通过如下命令进行查看：

  ```shell
  # otool -L MyApp.app/Contents/MacOS/MyApp
  
  MyApp.app/Contents/MacOS/MyApp:
  	libMyLib.1.dylib (compatibility version 1.0.0, current version 1.0.0)
  	@rpath/QtWidgets.framework/Versions/5/QtWidgets (compatibility version 5.12.0, current version 5.12.3)
  	@rpath/QtGui.framework/Versions/5/QtGui (compatibility version 5.12.0, current version 5.12.3)
  	@rpath/QtCore.framework/Versions/5/QtCore (compatibility version 5.12.0, current version 5.12.3)
  	/System/Library/Frameworks/DiskArbitration.framework/Versions/A/DiskArbitration (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/IOKit.framework/Versions/A/IOKit (compatibility version 1.0.0, current version 275.0.0)
  	/System/Library/Frameworks/OpenGL.framework/Versions/A/OpenGL (compatibility version 1.0.0, current version 1.0.0)
  	/System/Library/Frameworks/AGL.framework/Versions/A/AGL (compatibility version 1.0.0, current version 1.0.0)
  	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 800.6.0)
  	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1281.0.0)
  ```

  可以看到MyApp依赖于libMyLib.1.dylib 库，对于一些路径的变量如@rpath等可以百度进行了解，此处@rpath是Frameworks目录

  为了修复依赖我们需要以下几个步骤：

  - 创建Frameworks目录

    ```shell
    # mkdir -p MyApp.app/Contents/Frameworks
    ```

    

  - 拷贝第三方库到Frameworks目录

    ```shell
    # cp libMyLib.1.dylib MyApp.app/Contents/Frameworks/
    ```

    

  - 修复依赖关系

    ```shell
    # install_name_tool -change "libMyLib.1.dylib" "@rpath/libMyLib.1.dylib" MyApp.app/Contents/MacOS/MyApp
    ```

    （⚠️：如果某个库依赖于其他库，也需要用上述命令修复依赖关系）

  - 打包

    ```shell
    # macdeloyqt MyApp.app -dmg
    ```

    此时的APP就可以直接运行了，打包好的dmg文件可以拷贝给其他用户使用了。至此我们完成了打包的过程，接下来可以进行美化。

## 二、程序包美化

添加图标

- 右键bundle程序，显示包内容，进入Contents目录

- 修改Info.plist 

  ![mac3.png](http://nas.masterxx.top:10088/images/2021/05/23/mac3.png)

- 拷贝图标文件到Resources目录下

- 右键bundle程序，点击显示简介，拖拽图标到箭头位置

![mac4.png](http://nas.masterxx.top:10088/images/2021/05/23/mac4.png)

此时程序已经显示为我们想要显示的图标了。



### 三、 DMG美化

#### 1. DMG添加图标

DMG文件也是可以添加图标的，原理跟程序包一样

右键DMG文件，点击显示简介，拖拽图标到对应位置即可

#### 2. DMG添加背景图

如果要修改DMG背景图，之前生成的DMG文件就不能用了，因为是不可写的，那么就需要我们自己添加DMG文件。

- 生成空的DMG磁盘

  从launchpad中找到磁盘工具->文件->新建映像->空白映像

  ![mac5.png](http://nas.masterxx.top:10088/images/2021/05/23/mac5.png)

  填入信息，如下图：

  ![mac6.png](http://nas.masterxx.top:10088/images/2021/05/23/mac6.png)

- 双击打开生成的DMG文件，如下图

  ![mac7.png](http://nas.masterxx.top:10088/images/2021/05/23/mac7.png)

- 在打开的finder中，右键->查看显示选项，添加背景图片

  ![mac8.png](http://nas.masterxx.top:10088/images/2021/05/23/mac8.png)

#### 3. 添加应用程序到DMG

将之前生成的app拖拽到DMG下

![mac9.png](http://nas.masterxx.top:10088/images/2021/05/23/mac9.png)

#### 4. 添加应用程序快捷方式

打开系统目录，找到应用程序，生成替身，将替身拖拽到DMG下，并改下名称即可

![mac10.png](http://nas.masterxx.top:10088/images/2021/05/23/mac10.png)

#### 5. 压缩DMG文件

- 首先将上面打开的MyAPP.dmg弹出，打开磁盘工具->映像->转换

  ![mac11.png](http://nas.masterxx.top:10088/images/2021/05/23/mac11.png)

- 选择MyAPP.dmg

- 点击转换，DMG文件会根据APP的实际大小进行压缩

  ![mac12.png](http://nas.masterxx.top:10088/images/2021/05/23/mac12.png)

#### 6. 完善

- 修改转换后的DMG文件名
- 参考上述信息修改DMG图标

至此整个打包，美化已经完成了。



