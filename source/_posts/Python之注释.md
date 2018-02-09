---
title: Python注释问题
date: 2018-01-24 19:47:43
categories: python
tags: json
---

> 最近接手一python的小项目，添加一些功能。不过原来项目重复的代码使用使得强迫症瞬间加重，就动手整理一下。对于有些功能代码暂时用不到就先进行了注释，等最后完成之后如果完全不用了就删掉。 可是整理完提交代码，可是发现代码运行不到，检查也没有错误啊。就算有错误你好歹运行起来报错给我啊。没弄过python项目，一脸懵逼啊！

> 一开始以为是tab的问题，因为知道python对与这个有严格的要求。把调用的地方的tab都整理下竟然也不行，凡是方法导入下面的语句都无法运行。 最后把导入放到前面终于报错了，那么离解决问题就快了。 

<!--more-->

### 错误提示
**SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes**

度娘一下发现是之前注释的代码有问题，why？我都注释了还搞什么。。。

```
os.system(r"C:\\Users\\Victor\\Projects\\xpay_pp\\iview\\64\\i_view64.exe C:\\Users\\Victor\\Projects\\xpay_pp\\tt.jpg")

```
路径用\就会报错，换成\\或者/就没问题了。囧