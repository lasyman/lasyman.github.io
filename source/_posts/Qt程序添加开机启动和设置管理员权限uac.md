---
title: Qt程序添加开机启动和设置管理员权限uac
date: 2018-01-25 18:47:43
categories: Qt
tags: uac
---

> 为了系统服务或者方便快捷的使用某些程序，开机启动有的时候就成了程序必不可少的配备。当然有利也有弊，过多的程序开机启动会导致系统启动变慢。
> 程序进行一些操作需要管理员权限，如果直接给程序添加这样的权限，使用起来就会更加方便。

<!--more-->

# 本文就介绍Qt程序如何实现开机启动和设置管理员权限。

## 
### 开机启动设置
开机启动一般是通过修改注册表的方式完成的，Qt程序也不例外。
```
#define REG_RUN "HKEY_CURRENT_USER\\Software\\Microsoft\\Windows\\CurrentVersion\\Run"
void setAutoStart(bool bAutoStart)
{
    QString application_name = QApplication::applicationName();
    QSettings *settings = new QSettings(REG_RUN, QSettings::NativeFormat); 
    if(bAutoStart)
    {
		// 写入注册表信息，开机启动
        QString application_path = QApplication::applicationFilePath();
        settings->setValue(application_name, application_path.replace("/", "\\"));
    }
    else
    {
		// 移除注册表信息，禁止开机启动
        settings->remove(application_name);
    }
    delete settings;
}
```

### 设置管理员权限
何为程序拥有管理员权限？即程序运行的时候不需要右键->以管理员权限运行就可默认以此权限运行
1. 在VS2008以后，配置比较简单，工程右键->属性->链接器->清单文件->UAC执行级别
（requireAdministrator）即可，当然此时vs需要以管理员权限运行起来。
2. Creator配置需要在pro中添加：
QMAKE_LFLAGS += /MANIFESTUAC:\"level=\'requireAdministrator\' uiAccess=\'false\'\" #以管理员运行
然后以管理员身份运行Creator，重新编译即可。
3. 通过命令赋予执行程序管理员权限
新建一个名为uac.manifest的文档
```
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>  
<assembly xmlns='urn:schemas-microsoft-com:asm.v1' manifestVersion='1.0'>  
  <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">  
    <security>  
      <requestedPrivileges>  
        <requestedExecutionLevel level='requireAdministrator' uiAccess='false' />  
      </requestedPrivileges>  
    </security>  
  </trustInfo>  
</assembly>
```
执行命令
```
"mt.exe" -manifest "uac.manifest" -outputresource:"xxx.exe";#1
```
[uac.bat](http://ougipk3bp.bkt.clouddn.com/uac.bat "uac.bat")
[uac.manifest](http://ougipk3bp.bkt.clouddn.com/uac.manifest "uac.manifest")
[mt.exe](http://ougipk3bp.bkt.clouddn.com/mt.exe)

这样程序就会带上小盾牌了。