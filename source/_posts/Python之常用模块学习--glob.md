---

title: Python之常用模块学习--glob
date: 2020-02-12 12:28:15
categories: python
tags: python

---

# Python之常用模块学习--glob



> glob是python自己带的一个文件操作相关模块，用它可以查找符合自己目的的文件，支持通配符操作，*,?,[]这三个通配符，*代表0个或多个字符，?代表一个字符，[]匹配指定范围内的字符，如[0-9]匹配数字。使用此模块可以很方便的筛选我们需要的文件。

<!--more-->

glob模块有两个方法分别是glob和iglob，区别是glob获取的是目录下所有匹配的文件，返回是一个列表；iglob获取的目录下匹配文件，返回的是一个迭代器。

## 使用示例：

新建glob_t.py，并在当前目录下新建目录test，添加要匹配的文件如1.txt，2.txt，ads.txt等

### glob

```python
import glob

f = glob.glob("./test/" + "*.txt")
print(type(f))
print(f)

ff = glob.glob("./test/" + "[0-9].txt")
print(ff)

//输出结果是：
<class 'list'>
['./test/ads.txt', './test/1.txt', './test/2.txt']
['./test/1.txt', './test/2.txt']
```



### iglob

```python
import glob

f1 = glob.iglob("./test/" + "*.txt")
print(f1)
for file in f1:
    print(file)
    
//输出结果是：
<generator object _iglob at 0x108260c50>
./test/ads.txt
./test/1.txt
./test/2.txt
```

