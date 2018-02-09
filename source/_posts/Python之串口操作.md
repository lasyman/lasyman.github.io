---
title: Python串口操作
date: 2018-01-25 19:47:43
categories: python
tags: serial
---
> 最近接到一个项目是用Python写一个windows下的应用程序，能够通过串口打印机进行打印小票。之前用Qt操作过串口，那么在python下该如何做呢？

<!--more-->

## Python串口操作
Python是出了名的第三方库多，那做这个肯定少不了了。发现有默认的serial库已经有了基本操作，还有PySerial的扩展，那么做这个应该不是难题了。


```
# 导入对应的库
import serial
import serial.tools.list_ports

# 获取可用串口
def get_visiableComs():
	plist = list(serial.tools.list_ports.comports())
    coms = []
    if len(plist) <= 0:
        print("没有发现端口!")
    else:
        for plist_0 in plist:
            serialName = plist_0[0]
            serialFd = serial.Serial(serialName, 9600, timeout=60)
            print("可用端口名>>>", serialFd.name)
            coms.append(serialFd.name)
	return coms

# 串口写操作
def com_write(msg):
	port = 'COM3' # 串口号
	bd = 9600     # 波特率,一定要设置好对应的波特率，不然打印机会打印乱码
	serialPort = serial.Serial(port, bd, timeout=1)
	if serialPort.is_open:
		 b = bytes(msg, encoding='gbk') #中文支持

```