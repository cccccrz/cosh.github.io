---
title: Qt MVC模型
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 07:35:18/Qt MVC模型.html
mathJax: false
date: 2023-04-13 15:35:18
thumbnail:
---
> 介绍详见Qt文档 Model/View Programming
>
> 模型Model提供数据，视图View负责呈现模型数据，并负责处理用户输入。委托增加了输入的灵活性，提供输入功能，负责呈现视图中的单个项。
>
> <!-- more -->



### Model：创建自定义模型

------

> 详见Qt文档 Model/View Programming/Creating New Models

##### 自定义列表模型：子类化`QAbstractListModel`

1. 设置自定义列表数据

   `QList<T> datas_;`

2. 重写`rowCount()` 和 `data()`
   ```cpp
   // 计算列表总数
   virtual int rowCount(const QModelIndex &parent = QModelIndex()) const = 0;
   // 根据索引、自定义枚举 提取数据
   virtual QVariant data(const QModelIndex &index, int role = Qt::DisplayRole) const = 0;
   ```

3. 如果需要编辑，重写`setData()` 和 `removeRows()`

   ```cpp
   virtual bool setData(const QModelIndex &index, const QVariant &value, int role = Qt::EditRole);
    virtual bool removeRows(int row, int count, const QModelIndex &parent = QModelIndex());
   ```

4. 其他列表模型

   ```cpp
   // 为树和表视图提供标头信息
   virtual QVariant headerData(int section, Qt::Orientation orientation, int role = Qt::DisplayRole) const;
   // 分层模型必须实现
   virtual QModelIndex index(int row, int column, const QModelIndex &parent = QModelIndex()) const = 0;
   virtual QModelIndex parent(const QModelIndex &index) const = 0;
   ```

##### 自定义排序筛选模型：子类化` QSortFilterProxyModel`

> 排序筛选模型依赖**源**数据模型，根据指定规则进行排序筛选。
>
> 可以链接到任何类型的视图，并在视图之间共享。
>
> 每个数据是从源模型中的 **原始位置**，**映射** 到代理模型中的不同位置。
>
> 视图使用代理模型提供的模型索引访问项，这些索引不包含源模型原始项的位置信息（即使用代理模型的话，视图提供的索引信息都是代理的模型数据，即原始位置的映射）

1. 自定义筛选规则

   ```cpp
   virtual bool filterAcceptsRow(int source_row, const QModelIndex &source_parent) const;
   ```

2. 自定义排序规则

   ```cpp
   virtual bool lessThan(const QModelIndex &source_left, const QModelIndex &source_right) const;
   ```

</br>

### Delegate: 创建自定义委托

------

> 官方建议视图**不要共用**委托

##### 自定义Item委托，子类化`QStyledItemDelegate`

1. 根据索引画出item

   ```cpp
   virtual void paint(QPainter *painter, const QStyleOptionViewItem &option, const QModelIndex &index) const override;
   ```

2. 设置item大小

   ```cpp
   virtual QSize sizeHint(const QStyleOptionViewItem &option, const QModelIndex &index) const override;
   ```

3. 编辑事件，只包含鼠标事件，可以实现右键菜单或按钮点击等

   ```cpp
   virtual bool editorEvent(QEvent *event, QAbstractItemModel *model, const QStyleOptionViewItem &option, const QModelIndex &index) override;
   ```

</br>

### View：视图

------

继承`QAbstractItemView`的视图有一些默认信号

```cpp
signals:
    void activated(const QModelIndex &index);
    void clicked(const QModelIndex &index);
    void doubleClicked(const QModelIndex &index);
    void entered(const QModelIndex &index);
    void iconSizeChanged(const QSize &size);
    void pressed(const QModelIndex &index);
    void viewportEntered();
```

使用Qt自带的视图基本满足需求：`QListView`， `QTreeView`

</br>

### Example

------

demo源码：

[qt_demo/mvc_test at main · cccccrz/qt_demo (github.com)](https://github.com/cccccrz/qt_demo/tree/main/mvc_test)

![mvc_example.png](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/mvc_example.png)
