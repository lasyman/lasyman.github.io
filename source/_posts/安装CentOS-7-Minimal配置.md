---
title: CentOS-7-Minimal配置
date: 2019-11-10 10:47:43
categories: Linux
tags: Server
---



# 安装CentOS-7-Minimal配置

>Centos7 Minimal系统安装好系统之后网络是不可用的，所以要先配置网络



<!--more-->

## 一、准备工作

- 连接显示器、键盘
- 有线连接网络

## 二、网络配置

### 1）网络安装配置

- 查看网卡：

```shell
# ip addr
```

![网卡]( http://pics.wanjiabc.cn/工控机网络1.png )

此工控机有两个有线网络和一个无线网络，我们将网线插入LAN1，配置第一个网络

- 编辑第一个有线网口的配置：

```shell
# vi /etc/sysconfig/network-scripts/ifcfg-enp2s0
```

![网络2]( http://pics.wanjiabc.cn/工控机网络2.png )

- 重启网络服务

```shell
# service network restart
```

到这里，网络都已经配置好了，此时可以安装程序了。

### 2) 安装网络工具

此时网络可以使用，但是不能使用ifconfig命令等，需要安装网络工具

```shell
# yum -y install net-tools pciutils iw
```



### 3）静态IP配置

- 编辑网口的配置文件

  此处我们还使用LAN1

```shell
# vi /etc/sysconfig/network-scripts/ifcfg-enp2s0
```

修改配置文件如下：

![静态IP]( http://pics.wanjiabc.cn/工控机网络3.png )

- 重启网络服务

```shell
# service network restart
```

