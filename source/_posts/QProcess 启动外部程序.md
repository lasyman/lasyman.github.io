---
title: QProcess 启动外部程序
date: 2018-01-25 18:47:43
categories: Qt
tags: QProcess
---

> 程序有的时候需要调用外部程序来启动一些服务或者工具来起到辅助作用，那么在Qt下一般用QPorcess来完成启动外部程序，也可以进行交互通讯。
> 本文就介绍下通过QProcess启动外部程序的方法和遇到的问题以及交互通讯的方式。

<!--more-->
## 

### QProcess启动外部程序的方法
#### 1. 一体式
所谓一体式就是启动的外部程序会随着主程序的退出而退出,此方式一般是阻塞的，建议在线程中完成。
```
void QProcess::start(const QString & program, const QStringList & arguments, OpenMode mode = ReadWrite);
// 参数1：外部程序路径
// 参数2：外部程序参数
// 参数3：模式
```
#### 2. 分离式
所谓分离式是外部程序启动后，当主程序退出时，外部程序并不退出，而是继续运行。
```
void QProcess::startDetached(const QString & program, const QStringList & arguments, const QString & workingDirectory = QString(), qint64 * pid = 0);
// 参数1：外部程序路径
// 参数2：外部程序参数
// 参数3：工作目录
// 参数4：外部程序进程id
```
****
** 【注意】 ** 当程序路径中含有空格的时候，如果仅仅设置参数1是无法识别的
** 【解决办法】 ** 将参数1作为参数2传入
****
比如：
```
//1-不带空格，可以启动
process->start("C:/test/test.exe");

//2-带空格，无法启动
process->start("C:/Program Files/test/test.exe");

//3-带空格，使用带参模式，可以启动
process->start("C:/Program Files/test/test.exe", QStringList("C:/Program Files/test/test.exe"));
```
### 启动程序前的准备工作
- 设置工作目录
void setWorkingDirectory(const QString &dir);
- 设置运行环境
void setEnvironment(const QStringList &environment);
- 设置路径
void QProcess::setProgram(const QString & program);
- 设置参数【可选】
void QProcess:: setArguments(const QStringList & arguments);
void setNativeArguments(const QString &arguments);//如果上方法设置参数不生效的时候采用此方法
- 启动
void QProcess::start(OpenMode mode = ReadWrite);

其实start()和startDetached()已经整合了上述过程

### 启动状态
外部程序未启动时，其状态是NotRunning
当启动时，其状态转变为Starting，正在启动，但此时还未调用起来；
启动之后，继续变为Running，同时发射出started()信号，此时，可以对QProcess进行读写操作了；
当退出时，其状态改为NotRunning，并发射出finished()信号。finishe()信号会携带退出码和退出状态，可以分别通过exitCode()和exitStatus()来获得。
当发生错误时，QProcess会发出一个error()信号，同样的，也可以通过error()来获得其错误类型，通过state()获得当前程序的状态。

### 交互
QProcess有两种预定义的输出通道：标准输出stdout与标准错误stderr。
通过setReadChannel()可以选择当前读取输出的通道。
当通道中的数据准备就绪时，QProcess会发出readyRead()信号。如果是标准输出，则发出readyReadStandardOutput()信号；如果是标准错误，则发出readyReadStandardError()信号。
常用的读取方式有read(), readAll()或getChar()，也可以通过readAllStandardOutput()和readAllStandardError()读取标准输出和标准错误通道中的数据。

某些程序需要环境设置才能进行特殊的操作。可以通过setEnvironment()来设置环境变量，通过setWorkingDirectory()来设置工作目录，默认的工作路径是当前调用程序的工作路径。

### 同步进程API
QProcess提供了一系列的函数以提到事件循环来完成同步操作：
- waitForStarted()          : 阻塞，直到外部程序启动
- waitForReadyRead()    : 阻塞，直到输出通道中的新数据可读
- waitForBytesWritten()  : 阻塞，直到输入通道中的数据被写入
- waitForFinished()        : 阻塞，直到外部程序结束
如果在主线程（QApplication::exec()）中调用这些函数，可能会造成当前用户界面不响应。