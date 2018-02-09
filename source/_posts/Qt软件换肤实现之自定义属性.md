---
title: Qt软件换肤实现之自定义属性
date: 2017-06-23 19:47:43
categories: Qt
tags: 换肤
---

> 前面介绍了Qt实现软件换肤的流程，基本的换肤操作已经ok了。但是有些控件（如QAbstractItemModel、QAbstractItemDelegate更新表格样式的时候）无法通过指定的控件获取到样式或者原始属性无法满足需求，此时自定义属性就可以发挥作用了，通过自定义属性动态获取样式。
> 那么如何设置自定义属性并应用呢？

<!--more-->

## **本文介绍qss自定义属性设置与使用**

***qss只能用于QWidget及其子孙类***

- 一、创建一个继承于QWidget的类QStyledWidget
- 二、为QStyleWidget添加自定义属性，并使用Q_PROPERTY声明
- 三、自定义qss，并使用属性，语法：
```
qproperty-<property name>: "";//值可以为Json类型
```
- 四、 从qss获取并使用自定义属性
```
QStyledWidget->property("property name").toString();
//就可以取到qss内设置的值
```

**示例：**
QStyledWidget.h
```
#ifndef QSTYLED_WIDGET
#define QSTYLED_WIDGET

#include <QWidget>

class QStyledWidget : public QWidget
{
	Q_OBJECT
	Q_PROPERTY(QString customeProperty READ customeProperty WRITE setCustomeProperty)
public:
	explicit QStyledWidget(QWidget *parent = 0);
	~QStyledWidget();

	QString customeProperty() const;
	void setCustomeProperty(QString strProperty);
private:
	QString m_strProperty;
};

#endif //QSTYLED_WIDGET
```

QStyleWidget.cpp
```
#include <qstyledwidget.h>
QStyledWidget::QStyledWidget(QWidget *parent)
	:QWidget(parent)
{
}
QStyledWidget:~QStyledWidget()
{
}
QString customeProperty() const
{
	return m_strProperty;
}
void setCustomeProperty(QString strProperty)
{
	m_strProperty = strProperty;
}
```

