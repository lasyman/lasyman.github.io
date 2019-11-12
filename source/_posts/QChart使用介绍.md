---
title: QChart使用介绍
date: 2019-10-25 18:47:43
categories: Qt
tags: QChart
---



# QChart使用介绍

## 一、基本介绍

在Qt QChart机制里，实现一个图表显示需要满足几个要素：

1. Series 实例数据类型（如柱状图，曲线图等）
2. Axis 实例坐标轴（横坐标，纵坐标)
3. Chart 图表引擎 (承载图表数据，类似于QGraphicsScene)
4. ChartView 图表显示 (将图表数据展现出来，类似于QGraphicsView)

## 二、数据类型及API介绍

### 1. QSplineSeries 曲线图表

- 图表示例图

![QSplineSeries](http://pics.wanjiabc.cn/QSplineSeries.png)

- 主要API介绍

  ```c++
  //设置曲线的名字，用于legend展示
  void setName(const QString &name);
  
  //是否显示曲线标签（折点坐标）
  void setPointLabelsVisible(bool visible = true);
  
  //是否显示曲线折点
  void setPointsVisible(bool visible = true);
  
  //曲线标签显示格式
  void setPointLabelsFormat(const QString &format);
  //(default: setPointLabelsFormat("(@xPoint, @yPoint)");)
  ```

  

### 2. QScatterSeries 散点图表

- 图表示例图

![QScatterSeries](http://pics.wanjiabc.cn/QScatterSeries.png)

- 主要API介绍

```c++
//设置散点对象的名字，用于legend展示
void setName(const QString &name);

//设置散点的大小
void setMarkerSize(qreal size);

//设置散点的形状
void setMarkerShape(QScatterSeries::MarkerShape shape);

//是否显示散点标签（坐标）
void setPointLabelsVisible(bool visible = true);

//散点标签显示格式
void setPointLabelsFormat(const QString &format);
//(default: setPointLabelsFormat("(@xPoint, @yPoint)");)
```

### 3. QPieSeries 圆饼图表

- 图表示例图

  ![QPieSeries](http://pics.wanjiabc.cn/QPieSeries.png)

  ![QPieSeries](http://pics.wanjiabc.cn/QPieSeries1.png)

- 主要API介绍

```c++
//QPieSeries
//是否显示所有图表标签 (也可以单独设置)
void setLabelsVisible(bool visible = true);
//设置图表所有标签显示位置 (也可以单独设置)
void setLabelsPosition(QPieSlice::LabelPosition position);

//QPieSlice
//是否凸显出来
void setExploded(bool exploded = true);

```

