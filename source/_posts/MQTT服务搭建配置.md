---
title: MQTT服务搭建
date: 2018-01-04 17:47:43
categories: 服务器
tags: MQTT
---

> 前言：
>  MQTT是IBM开发的一个即时通讯协议。MQTT是面向M2M和物联网的连接协议，采用轻量级发布和订阅消息传输机制。Mosquitto是一款实现了 MQTT v3.1 协议的开源消息代理软件，提供轻量级的，支持发布/订阅的的消息推送模式，使设备对设备之间的短消息通信简单易用。
>  
若初次接触MQTT协议，可先理解以下概念：
【MQTT协议特点】——相比于RESTful架构的物联网系统，MQTT协议借助消息推送功能，可以更好地实现远程控制。
【MQTT协议角色】——在RESTful架构的物联网系统，包含两个角色客户端和服务器端，而在MQTT协议中包括发布者，代理器（服务器）和订阅者。
【MQTT协议消息】——MQTT中的消息可理解为发布者和订阅者交换的内容（负载），这些消息包含具体的内容，可以被订阅者使用。
【MQTT协议主题】——MQTT中的主题可理解为相同类型或相似类型的消息集合。

<!--more-->

## 一、 MQTT服务之Apache Apollo
Apache Apollo是一个代理服务器，其是在ActiveMQ基础上发展而来的，可以支持多种协议，如：STOMP、AMQP、MQTT、SSL等。
### 搭建环境
1. 虚拟机 Ubuntu 16.04.5 LTS
2. Apache-Apollo-1.7.1

### 准备工作
由于搭建Apollo环境变量需要有JAVA_HOME，这个时候需要安装JDK环境
1. 到官网下载Apache-Apollo-1.7.1 http://www.apache.org/dyn/closer.cgi?path=activemq/activemq-apollo/1.7.1/apache-apollo-1.7.1-unix-distro.tar.gz
或者通过命令下载
```
wget http://www.apache.org/dyn/closer.cgi?path=activemq/activemq-apollo/1.7.1/apache-apollo-1.7.1-unix-distro.tar.gz
```
2. 解压源码包
```
tar -zxvf apache-apollo-1.7.1-unix-distro.tar.gz
```

### 配置Apache-Apollo
进入解压目录下的bin目录

```
./apollo //执行后会显示如下帮助信息

usage: apollo [--log <log_level>] <command> [<args>]

The most commonly used apollo commands are:
    create           creates a new broker instance
    disk-benchmark   Benchmarks your disk's speed
    help             Display help information
    version          Displays the broker version

See 'apollo help <command>' for more information on a specific command.
```
1. 创建broker实例

```
./apollo create mybroker

Creating apollo instance at: mybroker
Generating ssl keystore...

You can now start the broker by executing:  

   "/home/***/Downloads/apache-apollo-1.7.1/bin/mybroker/bin/apollo-broker" run

Or you can setup the broker as system service and run it in the background:

   sudo ln -s "/home/***/Downloads/apache-apollo-1.7.1/bin/mybroker/bin/apollo-broker-service" /etc/init.d/
   /etc/init.d/apollo-broker-service start
```
2. 启动服务

```
./mybroker/bin/apollo-broker run
```

可以通过配置文件配置域名，端口，及其连接认证等信息
配置信息在创建的broker实例目录下mybroker/etc/apollo.xml

## 二、 MQTT服务之Mosquitto 
Mosquitto是一个实现了MQTT3.1协议的代理服务器，由MQTT协议创始人之一的Andy Stanford-Clark开发，它为我们提供了非常棒的轻量级数据交换的解决方案。

### 安装

#### 源码包方式

1. 获取源码包 http://mosquitto.org/files/source/
2. 解压后在源码目录里找到配置文件config.mk, 参数说明如下

```
# 是否支持tcpd/libwrap功能.  
#WITH_WRAP:=yes  
  
# 是否开启SSL/TLS支持  
#WITH_TLS:=yes  
  
# 是否开启TLS/PSK支持  
#WITH_TLS_PSK:=yes  
  
# Comment out to disable client client threading support.  
#WITH_THREADING:=yes  
  
# 是否使用严格的协议版本（老版本兼容会有点问题）  
#WITH_STRICT_PROTOCOL:=yes  
  
# 是否开启桥接模式  
#WITH_BRIDGE:=yes  
  
# 是否开启持久化功能  
#WITH_PERSISTENCE:=yes  
  
# 是否监控运行状态  
#WITH_MEMORY_TRACKING:=yes 
```

#### apt-get方式

```
sudo apt-get install mosquitto
```
* 这里需要注意的是，默认情况下Mosquitto的安装需要OpenSSL的支持；如果不需要SSL，则需要关闭config.mk里面的某些与SSL功能有关的选项（WITH_TLS、WITH_TLS_PSK）。接着，就是运行make install进行安装，完成之后会在系统命令行里发现mosquitto、mosquitto_passwd、mosquitto_pub和mosquitto_sub四个工具（截图如下），分别用于启动代理、管理密码、发布消息和订阅消息。

### 配置运行
安装完成之后，所有配置文件会被放置于/etc/mosquitto/目录下，其中最重要的就是Mosquitto的配置文件，即mosquitto.conf，以下是详细的配置参数说明。

```
# =================================================================  
# General configuration  
# =================================================================  
  
# 客户端心跳的间隔时间  
#retry_interval 20  
  
# 系统状态的刷新时间  
#sys_interval 10  
  
# 系统资源的回收时间，0表示尽快处理  
#store_clean_interval 10  
  
# 服务进程的PID  
#pid_file /var/run/mosquitto.pid  
  
# 服务进程的系统用户  
#user mosquitto  
  
# 客户端心跳消息的最大并发数  
#max_inflight_messages 10  
  
# 客户端心跳消息缓存队列  
#max_queued_messages 100  
  
# 用于设置客户端长连接的过期时间，默认永不过期  
#persistent_client_expiration  
  
# =================================================================  
# Default listener  
# =================================================================  
  
# 服务绑定的IP地址  
#bind_address  
  
# 服务绑定的端口号  
#port 1883  
  
# 允许的最大连接数，-1表示没有限制  
#max_connections -1  
  
# cafile：CA证书文件  
# capath：CA证书目录  
# certfile：PEM证书文件  
# keyfile：PEM密钥文件  
#cafile  
#capath  
#certfile  
#keyfile  
  
# 必须提供证书以保证数据安全性  
#require_certificate false  
  
# 若require_certificate值为true，use_identity_as_username也必须为true  
#use_identity_as_username false  
  
# 启用PSK（Pre-shared-key）支持  
#psk_hint  
  
# SSL/TSL加密算法，可以使用“openssl ciphers”命令获取  
# as the output of that command.  
#ciphers  
  
# =================================================================  
# Persistence  
# =================================================================  
  
# 消息自动保存的间隔时间  
#autosave_interval 1800  
  
# 消息自动保存功能的开关  
#autosave_on_changes false  
  
# 持久化功能的开关  
persistence true  
  
# 持久化DB文件  
#persistence_file mosquitto.db  
  
# 持久化DB文件目录  
#persistence_location /var/lib/mosquitto/  
  
# =================================================================  
# Logging  
# =================================================================  
  
# 4种日志模式：stdout、stderr、syslog、topic  
# none 则表示不记日志，此配置可以提升些许性能  
log_dest none  
  
# 选择日志的级别（可设置多项）  
#log_type error  
#log_type warning  
#log_type notice  
#log_type information  
  
# 是否记录客户端连接信息  
#connection_messages true  
  
# 是否记录日志时间  
#log_timestamp true  
  
# =================================================================  
# Security  
# =================================================================  
  
# 客户端ID的前缀限制，可用于保证安全性  
#clientid_prefixes  
  
# 允许匿名用户  
#allow_anonymous true  
  
# 用户/密码文件，默认格式：username:password  
#password_file  
  
# PSK格式密码文件，默认格式：identity:key  
#psk_file  
  
# pattern write sensor/%u/data  
# ACL权限配置，常用语法如下：  
# 用户限制：user <username>  
# 话题限制：topic [read|write] <topic>  
# 正则限制：pattern write sensor/%u/data  
#acl_file  
  
# =================================================================  
# Bridges  
# =================================================================  
  
# 允许服务之间使用“桥接”模式（可用于分布式部署）  
#connection <name>  
#address <host>[:<port>]  
#topic <topic> [[[out | in | both] qos-level] local-prefix remote-prefix]  
  
# 设置桥接的客户端ID  
#clientid  
  
# 桥接断开时，是否清除远程服务器中的消息  
#cleansession false  
  
# 是否发布桥接的状态信息  
#notifications true  
  
# 设置桥接模式下，消息将会发布到的话题地址  
# $SYS/broker/connection/<clientid>/state  
#notification_topic  
  
# 设置桥接的keepalive数值  
#keepalive_interval 60  
  
# 桥接模式，目前有三种：automatic、lazy、once  
#start_type automatic  
  
# 桥接模式automatic的超时时间  
#restart_timeout 30  
  
# 桥接模式lazy的超时时间  
#idle_timeout 60  
  
# 桥接客户端的用户名  
#username  
  
# 桥接客户端的密码  
#password  
  
# bridge_cafile：桥接客户端的CA证书文件  
# bridge_capath：桥接客户端的CA证书目录  
# bridge_certfile：桥接客户端的PEM证书文件  
# bridge_keyfile：桥接客户端的PEM密钥文件  
#bridge_cafile  
#bridge_capath  
#bridge_certfile  
#bridge_keyfile  
  
# 自己的配置可以放到以下目录中  
include_dir /etc/mosquitto/conf.d  
```

最后，启动Mosquitto服务很简单，直接运行命令行“mosquitto -c /etc/mosquitto/mosquitto.conf -d”即可开启服务。

## 测试
订阅
```
mosquitto_sub -h localhost -v -t mytopic
```
发布
```
mosquitto_pub -h localhost -t mytopic -m hello world
```