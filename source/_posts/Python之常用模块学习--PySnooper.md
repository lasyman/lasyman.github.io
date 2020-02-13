---

title: Python之常用模块学习--PySnooper
date: 2020-02-13 11:11:08
categories: python模块
tags: python

---

# Python之常用模块学习--PySnooper

> PySnooper是一个第三方库，官方介绍说PySnooper is a poor man's debugger。为什么这么说呢，因为平时我们调试Python代码的时候，一般都是在IDE中操作的，而且IDE一般都可以集成Python调试器，我们就可以在IDE中断点调试代码了。那么当我们没有调试器的时候，这个库就可以很方便的帮助我们调试我们的代码了。下面我们就去见识一下这个库吧。

<!--more-->

## 一、安装

通过pip，conda或者其他工具安装PySnooper

```python
pip install PySnooper

conda install -c conda-forge pysnooper
```

源码工具在[github](https://github.com/cool-RR/PySnooper)上

## 二、使用

### 1. 直接用于函数

就像一般的装饰器一样用于方法，运行过程中就会把方法中使用的变量的变化打印出来

```python
import pysnooper

@pysnooper.snoop()
def get_max(n_list):
    """
    获取数字列表中最大值
    :param n_list:
    :return:
    """
    m = 0
    for i in n_list:
        if i > m:
            m = i
    return m


num_list = [4, 2, 5, 9]
print("最大值是: %s" % get_max(num_list))
```

输出结果如下：

```
最大值是: 9
Source path:... /Users/allan/PycharmProjects/test-proj/pysnooper_t.py
Starting var:.. n_list = [4, 2, 5, 9]
11:33:42.815672 call         5 def get_max(n_list):
11:33:42.816110 line        11     m = 0
New var:....... m = 0
11:33:42.816148 line        12     for i in n_list:
New var:....... i = 4
11:33:42.816228 line        13         if i > m:
11:33:42.816284 line        14             m = i
Modified var:.. m = 4
11:33:42.816463 line        12     for i in n_list:
Modified var:.. i = 2
11:33:42.816537 line        13         if i > m:
11:33:42.816575 line        12     for i in n_list:
Modified var:.. i = 5
11:33:42.816621 line        13         if i > m:
11:33:42.816653 line        14             m = i
Modified var:.. m = 5
11:33:42.816681 line        12     for i in n_list:
Modified var:.. i = 9
11:33:42.816715 line        13         if i > m:
11:33:42.816750 line        14             m = i
Modified var:.. m = 9
11:33:42.816780 line        12     for i in n_list:
11:33:42.816814 line        15     return m
11:33:42.816846 return      15     return m
Return value:.. 9
```

可以看出PySnooper会把变量i 和 m值的变化打印出来，并且执行到哪一步也是可以清楚的看到。

### 2. 用于函数局部

```python
import pysnooper


def get_max(n_list):
    """
    获取数字列表中最大值
    :param n_list:
    :return:
    """
    m = 0
    for i in n_list:
        if i > m:
            m = i

    with pysnooper.snoop():
        lower = min(n_list)
        upper = max(n_list)
        mid = (lower + upper) / 2
    return m


num_list = [4, 2, 5, 9]
print("最大值是: %s" % get_max(num_list))
```



输出结果如下：

```
最大值是: 9
Source path:... /Users/allan/PycharmProjects/test-proj/pysnooper_t.py
New var:....... n_list = [4, 2, 5, 9]
New var:....... m = 9
New var:....... i = 9
11:47:16.907875 line        17         lower = min(n_list)
New var:....... lower = 2
11:47:16.908098 line        18         upper = max(n_list)
New var:....... upper = 9
11:47:16.908145 line        19         mid = (lower + upper) / 2
```

可以看出来，只有在定义with pysnooper.snoop()了，上面的for循环的变量调试是没有被打印出来的。这样我们就可以指定我们要调试的部门代码了，不用所有的调试信息都显示出来，方便我们查阅。



### 3. 定义调试信息到文件

```python
@pysnooper.snoop('/my/log/file.log')

with pysnooper.snoop('/my/log/file.log')
```

### 4. 高级用法

1. 调试信息添加前缀，这样输出的信息前面就会添加前缀，方便在日志中追踪

   ```python
   @pysnooper.snoop(prefix='ZZZ ')
   ```

2. 去除调试信息中的时间戳，堆栈等信息

   ```python
   @pysnooper.snoop(normalize=True)
   ```

3. 如果是多线程调用，那么添加线程信息就可以方便查看哪个线程在调用

   ```python
   @pysnooper.snoop(thread_info=True)
   ```

   

