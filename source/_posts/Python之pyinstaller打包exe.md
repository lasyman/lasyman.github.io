---
title: Python文件操作编码问题及解决办法
date: 2017-10-26 19:47:43
categories: python
tags: pyinstaller
---

> 用Python开发Windows应用程序，发布的时候就要打包成可执行程序。Python下提供了pyinstaller的库可以帮助我们完成Python程序的打包。当然还有其他的工具比如py2exe等也是可以的，但是打包后好像比较大，而且有些依赖需要自己处理，而pyinstaller就可以很好的解决这些问题。

<!--more-->

## pyinstaller如何打包windows下的Python程序

### 1. 下载安装pyinstaller
```
pip install pyinstaller
```
### 2. pyinstaller命令
```
usage: pyinstaller [-h] [-v] [-D] [-F] [--specpath DIR] [-n NAME]
                   [--add-data <SRC;DEST or SRC:DEST>]
                   [--add-binary <SRC;DEST or SRC:DEST>] [-p DIR]
                   [--hidden-import MODULENAME]
                   [--additional-hooks-dir HOOKSPATH]
                   [--runtime-hook RUNTIME_HOOKS] [--exclude-module EXCLUDES]
                   [--key KEY] [-d] [-s] [--noupx] [-c] [-w]
                   [-i <FILE.ico or FILE.exe,ID or FILE.icns>]
                   [--version-file FILE] [-m <FILE or XML>] [-r RESOURCE]
                   [--uac-admin] [--uac-uiaccess] [--win-private-assemblies]
                   [--win-no-prefer-redirects]
                   [--osx-bundle-identifier BUNDLE_IDENTIFIER]
                   [--runtime-tmpdir PATH] [--distpath DIR]
                   [--workpath WORKPATH] [-y] [--upx-dir UPX_DIR] [-a]
                   [--clean] [--log-level LEVEL]
                   scriptname [scriptname ...]
// 介绍主要的一些参数，其他的可以看帮助信息
-D 将生成的执行文件和依赖文件保存到一个目录
-w 执行过程中会不输出信息到终端
-F 执行文件和依赖文件打包在一起生成一个执行文件
-n 指定执行文件的名字
-i 指定执行文件的图标ico
```
**[注意:] 使用-F参数生成一个文件的话，执行过程中程序是会被解压到用户temp随机目录。这样如果有用到需要修改的配置文件则无法保存修改**
```
pyinstaller -D -w -i xx.ico -n xxx xxxx.py
```
执行上述命令之后，执行文件和依赖文件就会被生成到dist下xxx目录

### 3. 包含资源文件
当程序用到一些外部资源，比如一张图片或者配置文件，pyinstaller是不会检测到这些的。那么这些文件是需要我们指定打包进去的，当然你也可以在打包之后拷贝到目录里（-F参数生成单个文件就不行了）。 
上述命令执行完之后，会有对应名字的spec文件生成。如xxx.spec。通过文本编辑器打开xxx.spec,内容如下：
```
# -*- mode: python -*-

block_cipher = None

a = Analysis(['app.py'],
             pathex=['.\\workspace'],//项目目录
             binaries=[],
             datas=[],//资源文件
             hiddenimports=[],
             hookspath=[],
             runtime_hooks=[],
             excludes=[],
             win_no_prefer_redirects=False,
             win_private_assemblies=False,
             cipher=block_cipher)
pyz = PYZ(a.pure, a.zipped_data,
             cipher=block_cipher)
exe = EXE(pyz,
          a.scripts,
          exclude_binaries=True,
          name='xprint',
          debug=False,
          strip=False,
          upx=True,
          console=False , icon='xprint.ico')
coll = COLLECT(exe,
               a.binaries,
               a.zipfiles,
               a.datas,
               strip=False,
               upx=True,
               name='xprint')
```
里面的datas=[]就是需要包含的资源文件
资源文件语法结构如下
('','')
第一个引号问要引用资源文件的路径（可以是相对目录）
第二个引号是资源文件相对执行文件所在的目录
比如引用E:\test.png到执行文件目录的img目录下('E:\\test.png','img')。

修改完spec文件之后执行如下命令，依赖的资源文件就会被拷贝到dist/xxx目录下或者打包在一起生成一个文件
```
pyinstaller xxx.spec
```