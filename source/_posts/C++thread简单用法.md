---
title: C++ thread简单使用
date: 2018-10-30 09:23:19
categories: C++
tags: 
  C++
---
> 习惯了用Qt的各种封装，标准C++的好多用法都模糊了，在此简单记录下C++下thread的使用。

## C++ thread简单使用

语言不好表述，直接上段示例代码简单明了

<!--more-->

- 普通函数

```
#include <iostream>
#include <thread>

void th_fun1()
{
    std::cout << "th_fun1..." << std::endl;
    while (1);
}

void th_fun2(int a)
{
    std::cout << "th_fun2..." << a << std::endl;
    while (1);
}

int main(int argc, char *argv[])
{
    // 无参数
    std::thread t1(th_fun1);
    int i = 99;
    // 有参数
    std::thread t2(th_fun2, i);
    t1.join();
    t2.join();
    while (1);

    return 0;
}

```
运行结果如下：
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/thread1.png)

- 类成员函数

```
// Test.h
#ifndef TEST_H
#define TEST_H

#include <iostream>
class Test
{
public:
    Test();
    ~Test();
    void test_fun();
    void test_fun1(int a);
};
#endif //TEST_H
```

```
//Test.cpp
#include "Test.h"

Test::Test()
{
}


Test::~Test()
{
}

void Test::test_fun()
{
    std::cout << "Test::test_fun()..." << std::endl;
    while (1);
}

void Test::test_fun1(int a)
{
    std::cout << "Test::test_fun(int a): " << a << std::endl;
    while (1);
}
```

```
//main
#include <iostream>
#include <thread>

#include "Test.h"

int main(int argc, char *argv[])
{
    Test test;
    // 无参数
    std::thread t1(&Test::test_fun, &test);
    int i = 99;
    // 有参数
    std::thread t2(&Test::test_fun1, &test, i);
    t1.join();
    t2.join();
    while (1);

    return 0;
}
```

运行结果如下：
![](http://blog-res.oss-cn-hongkong.aliyuncs.com/pics/thread2.png)