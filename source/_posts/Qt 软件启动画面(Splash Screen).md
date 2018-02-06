---
title: Qt 软件启动画面(Splash Screen)
date: 2017-06-23 18:47:43
categories: Qt
tags: QSplashScreen
---

>有的软件启动的时候会涉及到插件，数据库等一系列加载过程，那如果把这些加载过程放到软件启动之后，可能因为一些阻塞行为或者用户的一些误操作导致软件卡顿或者无响应，这无疑会给用户一种非常不好的体验。
>那么过渡画面就很好的避免了这样的问题，因为过渡画面展示的过程中用户是无法进行操作的，但是为了避免显示呆板，可以将加载过程信息展示在上面，让用户看到软件是在工作的。
> 如此好的东西是如何实现的内，本文将介绍Qt下Splash Screen是如何实现的：

<!--more-->

- Qt中有封装好的Splash Screen类就是**QSplashScreen**, 
```
QSplashScreen(const QPixmap &pixmap = QPixmap(), Qt::WindowFlags f = Qt::WindowFlags())
```
默认的构造函数可以传入要显示的图片，也可以通过默认的构造函数，然后通过
```
void setPixmap(const QPixmap &pixmap);
```
方法加载图片，然后show出来。

- 虽然软件启动过程中通过这样一个类能正常显示启动画面，但是会看起来比较low。因为一般我们用过的一些软件，加载的时候会有一些信息展示出来，比如软件的版本信息以及授权信息等。那么如何通过这个类丰富这些内容呢？
- 默认提供如下的方法
```
void showMessage(const QString &message, int alignment = Qt::AlignLeft, const QColor &color = Qt::black)
```
参数：
	1. 要显示的字符信息
	2. 文字显示的位置，然而默认方法只能是左上，左下等这样的方向位置，并不能自定义位置，这样显示就很不方便了，有时候我们需要显示在图片对应的位置。
	3. 文字的颜色

- 默认方法不能个性化显示，但是QSplashScreen提供了一个可重载的方法
```
[virtual protected] void QSplashScreen::drawContents(QPainter *painter)
```
通过QPainter类画出要显示的内容，QPainter类中有如下方法可以使用：
```
void setPen(const QColor &color) //可以设置颜色
void setFont(const QFont &font) //可以设置文字类型大小等
void drawText(const QPoint &position, const QString &text) //可以在指定位置显示文字
```
这样我们就可以自定义我们需要的showMessage方法了。

- 示例：
```
// 接口
void drawMessage(const QPoint &p, const QColor &c, const QString &s)
{
	m_point = p;
	m_color = c;
	m_msg = s;
	repaint();
}
//@overwrite
virtual void drawContents(QPainter *painter)
{
	painter->setPen(m_color);
	QFont font("微软雅黑");//微软雅黑应该用对应的unicode码("\345\276\256\350\275\257\351\233\205\351\273\221")
	painter->setFont(font);
	painter->drawText(m_point, m_msg);
}
```
