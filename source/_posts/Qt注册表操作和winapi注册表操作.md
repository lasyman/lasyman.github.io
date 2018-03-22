---
title: Qt注册表操作和winapi注册表操作
date: 2018-03-21 15:20:05
categories: Qt
tags: 注册表
---

> 在windows下开发程序，有的时候就会要用到注册表操作。注册表就像一个系统的配置文件，供所有运行在系统中的程序使用。本文将介绍在Qt框架下操作注册表和通过win API操作注册表。

<!--more-->

## 一、 Qt下操作注册表
1. Qt下有操作配置文件和注册表的类QSettings，通过一个枚举表示操作的是注册表还是配置文件。

```
QSettings::NativeFormat 0
Store the settings using the most appropriate storage format for the platform. On Windows, this means the system registry; on macOS and iOS, this means the CFPreferences API; on Unix, this means textual configuration files in INI format.
QSettings::Registry32Format 2
Windows only: Explicitly access the 32-bit system registry from a 64-bit application running on 64-bit Windows. On 32-bit Windows or from a 32-bit application on 64-bit Windows, this works the same as specifying NativeFormat. This enum value was added in Qt 5.7.
QSettings::Registry64Format 3
Windows only: Explicitly access the 64-bit system registry from a 32-bit application running on 64-bit Windows. On 32-bit Windows or from a 64-bit application on 64-bit Windows, this works the same as specifying NativeFormat. This enum value was added in Qt 5.7.
QSettings::IniFormat 1
Store the settings in INI files.
QSettings::InvalidFormat 16
Special value returned by registerFormat().

```
2. 操作示例

```
QSetting *settings = new QSettings("注册表路径", QSettings::NativeFormat);
// 路径如"HKEY_CURRENT_USER\\Software"

// 获取值
QVarint var = settings->value("key");
// 修改值
settings->setValue("key", value);
```
3. 问题
value 可以是字符串，DWORD 但是QSettings无法写入binary， 此时可以通过win API进行binary值的写入


## 二、 通过win API操作注册表
1. 打开注册表

```
ERROR_SUCCESS == RegOpenKeyEx(HKEY_LOCAL_MACHINE, subKey, 0, KEY_ALL_ACCESS, &key)
```
2. 写入binary到注册表

```
HKEY key;
BYTE value[4];
memset(bt, 0, 4);
int nV = 0x000010A0;
memcpy(value, &nV, 4);
ERROR_SUCCESS == ::RegSetValueEx(key, L"key", 0, REG_BINARY, value, 4)
```
3. 关闭注册表

```
::RegCloseKey(key);
```