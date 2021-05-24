---
title: QGraphicsView拖拽问题
date: 2018-9-15 11:56:10
categories: Qt
tags: QGraphicsView
---

> 最近做项目需要用到Qt图形框架QGraphicsView，据说这个框架很强大，但是具体流畅性没有测试过，不过对于我的项目来说应该足够了。 
> 关于拖拽平移的问题经过一系列爬贴与研究终于搞定了，整理记录并分享一下。

<!--more-->

### 一、 通过centerOn()方法
网上大部分教程都是通过这种方式对view进行拖拽平移的, 代码如下：
```
void MyGraphicsView::mouseMoveEvent(QMouseEvent *event)
{
    if (m_bMouseTranslate){
        QPointF mouseDelta = mapToScene(event->pos()) - mapToScene(m_lastMousePos);
		// 根据当前 zoom 缩放平移数
    	delta *= m_scale;
    	delta *= m_translateSpeed;

    	// view 根据鼠标下的点作为锚点来定位 scene
    	setTransformationAnchor(QGraphicsView::AnchorUnderMouse);
    	QPoint newCenter(VIEW_WIDTH / 2 - delta.x(),  VIEW_HEIGHT / 2 - delta.y());
    	centerOn(mapToScene(newCenter));

    	// scene 在 view 的中心点作为锚点
    	setTransformationAnchor(QGraphicsView::AnchorViewCenter);
    }

    QGraphicsView::mouseMoveEvent(event);
}
```
效果：
![QGraphicsView_move1.gif](http://nas.masterxx.top:10088/images/2021/05/23/QGraphicsView_move1.gif)

这种方式能够平移拖拽但是必须是放大后才可以，我想要的效果是任何时候都可以，就类似于view无限大。

### 二、通过transform（）方法

```
void MyGraphicsView::mouseMoveEvent(QMouseEvent *event)
{
    if (m_bMouseTranslate){
        QPointF mouseDelta = mapToScene(event->pos()) - mapToScene(m_lastMousePos);
        QTransform old_transform = transform();
        QRectF scene_rect = scene()->sceneRect();
        QRectF new_scene_rect(scene_rect.x() - mouseDelta.x(),
                              scene_rect.y() - mouseDelta.y(),
                              scene_rect.width(), scene_rect.height());
        scene()->setSceneRect(new_scene_rect);
        setTransform(old_transform);
    }

    QGraphicsView::mouseMoveEvent(event);
}
```
效果如下:
![QGraphicsView_move2.gif](http://nas.masterxx.top:10088/images/2021/05/23/QGraphicsView_move2.gif)