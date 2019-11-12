---
title: Ubuntu 16.04 Mysql 安装配置
date: 2018-11-16 14:44:10
categories: Ubuntu
tags: 
  Ubuntu
---

> 最近使用Python写个脚本访问mysql数据库，遇到的一些坑，记录一下

## Ubuntu 16.04 Mysql 安装配置

<!--more-->

### 1. 安装
```
apt-get install mysql-server
```

### 2. 配置

#### 2.1 修改配置文件
```
vi /etc/mysql/mysql.conf.d/mysqld.cnf

#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0

```

#### 2.2 配置远程访问

```
mysql> use mysql;
Database changed
mysql> grant all privileges  on *.* to root@'%' identified by "password";
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> select Host,User,authentication_string from user;
+--------------+------+-------------------------------------------+
| host         | user | password                                  |
+--------------+------+-------------------------------------------+
| localhost    | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
| 192.168.1.1 | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
| %            | root | *A731AEBFB621E354CD41BAF207D884A609E81F5E |
+--------------+------+-------------------------------------------+
3 rows in set (0.00 sec)
```

#### 2.3 配置防火墙
```
ufw allow 3306
```

#### 2.4 重启Mysql
```
/etc/init.d/mysql restart
```

