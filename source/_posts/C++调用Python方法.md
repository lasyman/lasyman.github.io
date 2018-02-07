---
title: C++调用Python方法
date: 2017-12-26 17:47:43
categories: C++
tags: 
  C++,
  Python
---
> Python有着丰富的第三方库，开发效率之高是毋庸置疑的，C++/C的语言性能之快也是让人羡慕的。那么如果C++可以调用Python方法，可以更多的提高开发效率。

<!--more-->
** C++调用Python方法介绍 **
本文环境Win10， Python3.4

##### 一、 C++调用Python方法
- 定义Python方法
```
// hello.py
def hello():
	print("Hello python")
def hello1(args):
	print("Hello %s" %args)
```
- C++配置
1. 引用安装目录下的头文件目录
2. 链接对应的lib库 python34.lib 

- C++调用Python
```
int main(int argc, char* argv[])
// 1.初始化python模块
Py_Initialize();
if (!Py_IsInitialized() )
	return -1;
// 2.导入Python模块hello.py
PyObject* pModule = PyImport_ImportModule("hello");
if (!pModule)
{
	cout << ("Cant open python file!\n");
	return -1;
}
// 3.获取模块中的方法hello
PyObject* pFunhello = PyObject_GetAttrString(pModule,"hello");
if(!pFunhello)
{
	cout << "Get function hello failed";
	return -1;
}
// hello1 方法带有参数
PyObject* pFunhello1 = PyObject_GetAttrString(pModule,"hello1");
if(!pFunhello1)
{
	cout << "Get function hello1 failed";
	return -1;
}
// 4. 调用hello方法
PyObject_CallFunction(pFunhello,NULL);
// 调用带参数的hello方法
PyObject_CallFunction(pFunhello,"s", "python");
// 参数说明：
// a. 方法对象
// b. 参数类型(s 字符串参数，i 数字参数，o PyObject参数...)
// 如果是多个参数(ss, ii, oo)
// c. 参数内容
//
// 5. 结束释放Python
Py_DecRef(pModule);
Py_DecRef(pFunhello);
Py_DecRef(pFunhello1);
Py_Finalize();
return 0;
```

##### 二、 C++调用Python类
Python3中移除了PyInstance_New 暂未找到对应方法实现调用类

##### 三、 C++执行Python文件
前提是Python中有定义main入口并调用方法
- 修改hello.py如下
```
// hello.py
def hello():
	print("Hello python")
def hello1(args):
	print("Hello %s" %args)
if __name__ == '__main__':
	hello()
	hello("python")
```

- C++main函数调用
```
int main(int argc, char* argv[])
{
	Py_Initialize();
    if ( !Py_IsInitialized() )
    	return -1;

    PyObject *obj = Py_BuildValue("s", "hello.py");
    FILE *fp = _Py_fopen_obj(obj, "r+");
    if (fp == NULL)
        return -1;
    PyRun_SimpleFile(fp, "hello.py");

    Py_DecRef(obj);
    Py_Finalize();
}
```

参考：
[[C++/Python] 如何在C++中使用一个Python类? (Use Python-defined class in C++)](http://www.cnblogs.com/lancelod/p/4036922.html)