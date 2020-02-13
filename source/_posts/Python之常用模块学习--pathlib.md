---

title: Python之常用模块学习--pathlib
date: 2020-02-13 12:29:17
categories: python模块
tags: python

---

# Python之常用模块学习--pathlib

> 平时我们开发的时候肯定会遇到路径相关的使用，Windows下路径一般是\，而Linux相关的路径一般是/。那么我们一般做跨平台开发的时候对于路径处理就要很小心了，因为路径的错误可能就会导致莫名其妙的问题。常用的解决办法就是判断平台类型，通过条件判断用os.path进行适配。有没有更好更便捷的办法呢？

<!--more-->

pathlib是Python3.4之后引入的的模块，使用这个模块就可以很方便的处理路径文件而不用担心不同平台分隔符的问题。接下来我们就看看怎么使用吧。

## 一、基本用法示例

```python
from pathlib import Path

# 获取文件路径
p = Path(__file__)
# 获取文件所在目录
file_path = p.parent
print(file_path)

# 拼接目录
path2 = file_path / "test"
path3 = path2 / "test2"
print("path2: " + str(path2))
print("path3: " + str(path3))

# 判断是否存在
print("path2 exists: " + str(path2.exists()))
print("path3 exists: " + str(path3.exists()))

# 相对目录
pp = Path(".")
# 拼接目录
path4 = pp / "test" / "test1"
print("path4: " + str(path4))
# 获取绝对路径
print("path4: " + str(path4.resolve()))

# 遍历目录
print([x for x in pp.iterdir() if x.is_dir()])

```

输出结果如下：

```
/Users/allan/PycharmProjects/test-proj
path2: /Users/allan/PycharmProjects/test-proj/test
path3: /Users/allan/PycharmProjects/test-proj/test/test2
path2 exists: True
path3 exists: False
path4: test/test1
path4: /Users/allan/PycharmProjects/test-proj/test/test1
[PosixPath('test'), PosixPath('venv'), PosixPath('.idea')]
```



## 二、常用方法

```python
Path.is_dir()  # 判断是否是目录
Path.resolve() # 返回绝对路径
Path.open()    # 打开文件
Path.exists()  # 是否存在
Path.iterdir() # 遍历目录的子目录或文件
Path.with_suffix() # 更改路径后缀
Path.joinpath() # 拼接路径
Path.stat()    # 返回路径信息(同`os.stat()`)
Path.mkdir()   # 创建目录
Path.rename()  # 重命名路径
Path.root      # 返回路径的根目录
Path.parent   # 返回所有上级目录
Path.drive     # 返回驱动器名称
```



通过pathlib这个模块，路径和文件的操作就变得很简单了。

## 三、文档参考

https://docs.python.org/3/library/pathlib.html

