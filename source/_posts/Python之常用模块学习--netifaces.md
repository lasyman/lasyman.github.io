---

title: Python之常用模块学习--netifaces
date: 2020-02-12 12:45:41
categories: python模块
tags: python

---

# Python之常用模块学习--netifaces

> 平常做网络相关开发的时候，会经常用到网卡的相关信息，那么此模块netifaces就可以很方便的获取如`ip`地址, `MAC`地址等等信息，支持`Windows`, `Linux`和`MacOS`.

<!--more-->

### 一、安装netifaces

netifaces模块属于第三方模块，需要进行安装

```python
pip install netifaces
```

### 二、使用示例

- 1、获取当前主机的网卡设备

```python
import netifaces

interfaces = netifaces.interfaces()

print(interfaces)

# 输出结果：
['XHC20', 'lo0', 'gif0', 'stf0', 'en0', 'utun0', 'utun1', 'utun2']
（因为本机是Mac 系统并且安装和虚拟机，显示会比较多）
```

- 2、获取当前主机的指定网卡的信息

```python
import netifaces

inter_info = netifaces.ifaddresses('en0')
print(inter_info)

# 输出结果：
{18: [{'addr': '1c:1b:0d:77:ab:44'}], 30: [{'addr': 'fe80::c79:a8ef:8bbe:b18d%en0', 'netmask': 'ffff:ffff:ffff:ffff::/64', 'flags': 1024}], 2: [{'addr': '172.16.0.140', 'netmask': '255.255.0.0', 'broadcast': '172.16.255.255'}]}

（此处显示的信息类似我们在终端用ifconfig命令得到的输出，此处结果为字典类型，之后就可以通过此处信息获取具体内容）

```

- 3、获取当前主机Mac地址和IP地址

在上述例子中我们已经获取了指定网卡的所有信息，那么获取需要的信息就只需要在结果中取对应值就可以了

比如获取网口en0的Mac地址和IP地址：

```python
import netifaces

mac_addr = inter_info[netifaces.AF_LINK][0]["addr"]
IP_addr = inter_info[netifaces.AF_INET][0]["addr"]
print("Mac地址：" + mac_addr)
print("IP地址：" + IP_addr)

# 输出结果：
Mac地址：1c:1b:0d:77:ab:44
IP地址：172.16.0.140
```



### 三、附录

以下是上面取值用到的类型定义，也可以根据结果直接填写数字，用定义可以看起来更易于阅读

```

AF_APPLETALK = 16
AF_CCITT = 10
AF_CHAOS = 5
AF_CNT = 21
AF_COIP = 20
AF_DATAKIT = 9
AF_DECnet = 12
AF_DLI = 13
AF_ECMA = 8
AF_HYLINK = 15
AF_IMPLINK = 3
AF_INET = 2
AF_INET6 = 30
AF_IPX = 23
AF_ISDN = 28
AF_ISO = 7
AF_LAT = 14
AF_LINK = 18
AF_NATM = 31
AF_NDRV = 27
AF_NETBIOS = 33
AF_NS = 6
AF_PPP = 34
AF_PUP = 4
AF_ROUTE = 17
AF_SIP = 24
AF_SNA = 11
AF_SYSTEM = 32
AF_UNIX = 1
AF_UNSPEC = 0

IN6_IFF_AUTOCONF = 64
IN6_IFF_DYNAMIC = 256
IN6_IFF_OPTIMISTIC = 512
IN6_IFF_SECURED = 1024
IN6_IFF_TEMPORARY = 128

```



