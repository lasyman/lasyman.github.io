---
title: Ubuntu安装java环境
date: 2018-01-04 17:47:43
categories: 服务器
tags: ubuntu
---

> 有些服务的运行需要依赖于java运行环境，那么如何在Ubuntu下安装java运行环境呢?

<!--more-->

### 开发环境
Ubuntu 16.04 LTS

### 准备工作
1. 到官网下载源码包 http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
得到压缩包 jdk-8u151-linux-x64.tar.gz
2. 创建安装目录
```
sudo mkdir -p /usr/local/java
```
3. 解压源码包到创建的目录
```
sudo tar -zxvf jdk-8u151-linux-x64.tar.gz -C /usr/local/java
```
4. 配置环境变量
```
vim ~/.bashrc
// 在文件末尾添加如下内容
export JAVA_HOME=/usr/local/java/jdk1.8.0_151  
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
```
5. 使环境变量生效
```
source ~/.bashrc
```
6. 检查java环境变量
```
java -version
```
如果能够正常输出版本信息则安装成功。