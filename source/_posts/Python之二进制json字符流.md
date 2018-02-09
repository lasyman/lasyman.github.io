---
title: Python处理json二进制数据
date: 2018-01-25 19:47:43
categories: python
tags: json
---

> 在B/S或者C/S架构下，免不了跟服务器通讯，现在比较流行的就是json格式，操作简单，数据直观。
> 当Python版本低于3.6的时候，接收的json字符串流无法直接转为json对象。
<!--more-->

## Python处理json二进制数据

1. 正常情况我们获取到json数据后会将其转为json对象，然后进行取值操作
```
import json
jsonObj = json.loads(jsonStr)
value = jsonObj[key]
```
2. 但是我在3.4版本收到的json是二进制的时候这么处理就会报错

收到的数据如下，开头的b表示二进制流
```
 msg = b'{\n"cmd":"print,\n"ticket_info":\n{\n\t"ticket_urls":"xxxx"}'
```

** 那么这种json数据就需要进行编码处理后操作了, json一般为utf-8格式**
```
import json

info = str(msg, encoding='utf-8')
jsonObj = json.loads(info)
value = jsonObj['cmd']
print(value)

>> 输出为 print
```