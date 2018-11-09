---
title: C++ thread简单使用
date: 2018-10-30 09:23:19
categories: C++
tags: 
  C++
---

## C++11之std::function和std::bind 使用

### 实现延时调用：

<!--more-->

```
#include<iostream>// std::cout
#include<functional>// std::function

void func(void)
{
    std::cout << __FUNCTION__ << std::endl;
}

class Foo
{
public:
    static int foo_func(int a)
    {
        std::cout << __FUNCTION__ << "(" << a << ") ->: ";
        return a;
    }
};

class Bar
{
public:
    int operator() (int a)
    {
        std::cout << __FUNCTION__ << "(" << a << ") ->: ";
        return a;
    }
};

int main()
{
    // 绑定普通函数
    std::function<void(void)> fr1 = func;
    fr1();

    // 绑定类的静态成员函数
    std::function<int(int)> fr2 = Foo::foo_func;
    std::cout << fr2(100) << std::endl;

    // 绑定仿函数
    Bar bar;
    fr2 = bar;
    std::cout << fr2(200) << std::endl;
    while (1);
    return 0;
}
```
由上边代码定义std::function<int(int)> fr2，那么fr2就可以代表返回值和参数表相同的一类函数。可以看出fr2保存了指代的函数，可以在之后的程序过程中调用。这种用法在实际编程中是很常见的。

std::bind用来将可调用对象与其参数一起进行绑定。绑定后可以使用std::function进行保存，并延迟到我们需要的时候调用：

　　（1） 将可调用对象与其参数绑定成一个仿函数；

　　（2） 可绑定部分参数。

　　在绑定部分参数的时候，通过使用std::placeholders来决定空位参数将会是调用发生时的第几个参数。

```
#include<iostream>// std::cout
#include<functional>// std::function

class A
{
public:
    int i_ = 0; // C++11允许非静态（non-static）数据成员在其声明处（在其所属类内部）进行初始化

    void output(int x, int y)
    {
        std::cout << x << "" << y << std::endl;
    }

};

int main()
{
    A a;
    // 绑定成员函数，保存为仿函数
    std::function<void(int, int)> fr = std::bind(&A::output, &a, std::placeholders::_1, std::placeholders::_2);
    // 调用成员函数
    fr(1, 2);

    // 绑定成员变量
    std::function<int&(void)> fr2 = std::bind(&A::i_, &a);
    fr2() = 100;// 对成员变量进行赋值
    std::cout << a.i_ << std::endl;


    return 0;
}
```

### 回调使用
- 普通回调函数

```
#include<iostream>// std::cout
#include<functional>// std::function

void callBackP(int p)
{
    std::cout << __FUNCTION__ << " " << p;
}

class A
{
public:
    int i_ = 99; // C++11允许非静态（non-static）数据成员在其声明处（在其所属类内部）进行初始化

    void output(std::function<void(int)> c)
    {
        m_callBack = c;
        m_callBack(i_);
    }
    std::function<void(int)> m_callBack;
};

int main()
{
    // 普通回调函数
    A a;
    a.output(callBackP);

    while (1);
    return 0;
}
```

输出结果为：
callBackP 99

- 类成员函数为静态的

```
#include<iostream>// std::cout
#include<functional>// std::function

class B
{
public:
    static void callBack(int b)
    {
        std::cout << __FUNCTION__ << ": " << b;
    }
};

class A
{
public:
    int i_ = 99; // C++11允许非静态（non-static）数据成员在其声明处（在其所属类内部）进行初始化

    void output(std::function<void(int)> c)
    {
        m_callBack = c;
        m_callBack(i_);
    }
    std::function<void(int)> m_callBack;
};

int main()
{
    A a;
	a.output(&B::callBack，i);
    while (1);
    return 0;
}
```

输出结果为：
B::callBack : 99

- 普通类成员函数

```
#include<iostream>// std::cout
#include<functional>// std::function

class B
{
public:
    void callBack(int b)
    {
        std::cout << __FUNCTION__ << ": " << b;
    }
};

class A
{
public:
    int i_ = 99; // C++11允许非静态（non-static）数据成员在其声明处（在其所属类内部）进行初始化

    void output(std::function<void(int)> c)
    {
        m_callBack = c;
        m_callBack(i_);
    }
    std::function<void(int)> m_callBack;
};

int main()
{
    A a;
	B b;
	a.output(std::bind(&B::callBack, &b, std::placeholders::_1/*参数个数*/);
    while (1);
    return 0;
}
```
输出结果为：
B::callBack : 99