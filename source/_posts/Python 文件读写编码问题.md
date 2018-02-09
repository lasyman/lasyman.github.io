---
title: Python文件操作编码问题及解决办法
date: 2017-10-26 19:47:43
categories: python
tags: 编码
---

> 编码问题是编程中常见且最令人头痛的问题，特别是遇到中文的时候。

<!--more-->

**Python文件操作编码问题及解决办法**

#### 一、UnicodeDecodeError: 'gbk' codec can't decode byte 0xae in position 23: illegal multibyte sequence
操作代码：
```
file = open("filename",'r')
print(file.read())
```
此时文件我保存为UTF-8编码的，但是读取的时候用的gbk，应该是Python默认读取的时候采用的是系统编码。

解决办法：
- 1 采用二进制的形式进行读取
```
file = open("filename",'rb')
print(file.read())
```
- 2 打开的时候设置编码
```
import codecs
file = codecs.open("filename", 'r', 'utf-8')
print(file.read())
```