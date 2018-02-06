---
title: Qt Log4使用
date: 2017-06-23 18:47:43
categories: Qt
tags: log4qt
---

### Log4Qt 使用
## 1、获取Log4Qt源码
从github拉去Log4Qt源码
```
git clone https://github.com/devbean/log4qt
```
<!--more-->

## 2、单个模块使用Log4Qt
- 新建一个Qt工程，将Log4Qt的src目录拷贝到工程目录下
- 修改工程的pro文件，添加如下代码<br>
```
include(./src/log4qt/log4qt.pri)
```

- 引入头文件
```

include "log4qt/rollingfileappender.h"<br>
include "log4qt/logger.h"<br>
include "log4qt/patternlayout.h"<br>
include "log4qt/propertyconfigurator.h"<br>
include "log4qt/helpers/properties.h"<br>
```
- 配置输出格式和日志追加属性
```
PatternLayout *p_layout = new PatternLayout();
p_layout->setConversionPattern("%d [%p] %m%n");
// %d 输出日期时间
// %p 日志级别  （debug, info, warn)
// %m 日志信息
// %n 换行
// %c appender名字
RollingFileAppender *p_appender = new RollingFileAppender(p_layout, "logfilePath", true);
// 参数1 模板格式
// 参数2 日志文件路径，默认当前目录
// 参数3 是否追加
p_appender->activateOptions();
Logger *p_logger = Logger::logger(moduleName);
p_logger->addAppender(p_appender);

p_logger->debug("HHHHHHH");
```

## 3、多个模块同时使用日志
#### A、通过代码方式
配置properties // 控制日志的参数

可以定义以下结构体
```
struct logItem
{
    logItem() {}
    logItem(QString module)
    {
        strAppender = appender;
        //! 日志参数
        QString strLogFile = QString("log/%2.log").arg(module);
        Logger *logger = Logger::logger(module);

        PatternLayout *layout = new PatternLayout();
        layout->setConversionPattern("%d [%p] %m%n");
        RollingFileAppender *appender = new RollingFileAppender(layout, strLogFile,true);
        appender->setMaxBackupIndex(10);
        appender->setMaximumFileSize(20000000);
        //appender->setImmediateFlush(false);//是否立刻刷新
        appender->activateOptions();
        logger->addAppender(appender);
        logger->setAdditivity(false);
    }
    QString strAppender;
};
```
可以将多个日志通过name映射起来
```
QMap<int, logItem> LogModules;
logItem item1(module1);
LogModules.insert(module1, item);
logItem item2(module2);
LogModules.insert(module2, item2);
```
写日志操作
```
void WriteLog(QString module, QString msg)
{
    Logger *logger = Logger::logger(LogModules.value(module).strAppender);
    logger->debug(msg);
}
```

#### B、读取日志配置文件方式
单个配置文件格式如下，多个日志的话更改模块名拷贝一份即可
```
#<0>根日志
log4j.rootlogger=DEBUG,default
log4j.appender.default=org.apache.log4j.RollingFileAppender
log4j.appender.default.file=log/default.log
log4j.appender.default.maxFileSize=20000000
log4j.appender.default.maxBackupIndex=10
log4j.appender.default.layout=org.apache.log4j.PatternLayout
log4j.appender.default.layout.ConversionPattern=[%d] [%p] %m%n
log4j.appender.default.appendFile=true
#<1>模块
log4j.logger.lht_auth=DEBUG, lht_auth
log4j.appender.lht_auth=org.apache.log4j.RollingFileAppender
log4j.appender.lht_auth.file=log/lht_auth.log
log4j.appender.lht_auth.maxFileSize=20000000
log4j.appender.lht_auth.maxBackupIndex=10
log4j.appender.lht_auth.layout=org.apache.log4j.PatternLayout
log4j.appender.lht_auth.layout.ConversionPattern=[%d] [%p] %m
log4j.appender.lht_auth.appendFile=true

...
```
// lht_auth为模块名称 

读取配置文件写日志
```
PropertyConfigurator::configure(cfgFile);
WriteLog(module, msg);
```

