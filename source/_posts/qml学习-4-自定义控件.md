---
title: qml学习-4-自定义控件
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-08-07 08:57:13/qml学习-4-自定义控件.html
mathJax: false
date: 2023-08-07 16:57:13
---
>参考[Customizing Qt Quick Controls](https://doc.qt.io/qt-5/qtquickcontrols2-customize.html)
<!-- more -->

QtQuick控件由项目的层次结构(树)组成。所有可视项(Item)都可以替换成自定义项(Item)。
</br>
### **控件基类** `Control`
Control是QML 控件的抽象基类，基本的布局如下
![Control layout](https://doc.qt.io/qt-5/images/qtquickcontrols2-control.png)
自定义时需要指定控件的 `implicitWidth` 和 `implicitHeight`，这两个属性表示控件的隐式大小，即默认大小。其次，根据需求增加内边距 `padding` 。
`Control` 拥有两个可视项：`background` 和 `contentItem`，即所有继承 `Control` 的控件都可以自定义这两个Item。

#### **背景** `background`

拥有 `background: Item` 属性的控件可以自定义背景。
一般使用矩形控件自定义，也可以使用别的控件。

```qml
background: Rectangle {
    // 默认大小
    implicitWidth: 100
    implicitHeight: 40
    // 可以根据控件状态设置颜色、阴影等
    color: "#f6f6f6"
    border.color: "#26282a"
    border.width: 1
    radius: 4
    // 阴影
    layer.enabled: true
    layer.effect: DropShadow {
        verticalOffset: 1
        color: "#330066ff"
        samples: 10
        spread: 0.5
    }
}
```
一般控件的状态有 `pressed` 、`hovered` 、`checked` 等等，可以根据状态动态绑定颜色，实现高亮效果。
```qml
Button {
    /// ...
    property bool showHighLight:        pressed | hovered | checked
    property color backColor:           "gainsboro"
    property color backHighLightColor:  "white"
    /// ...
    background: Rectangle {
        /// ...
        color: showHighLight ?  backHighLightColor : backColor
        /// ...
    }
}
```
</br>

#### **内容项** `contentItem`

拥有 `contentItem: Item` 属性的控件可以自定义可视的内容项，十分自由。
简单的，可以直接使用 `Text`， `Rectangle` 基本控件；复杂的，也可以使用自定义布局，内容是完全自定义的。
比如增加自定义图片：
```qml
contentItem: Item {
    Image {
    /// ...
    }
    Text {
    /// ...
    }
}
```
</br>

### **自定义控件**

一些控件拥有特殊的部件。有时间完善。