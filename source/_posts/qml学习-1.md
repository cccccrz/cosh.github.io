---
title: qml学习-1
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-05-10 03:26:24/qml学习-1.html
mathJax: false
date: 2023-05-10 11:26:24
thumbnail:
---
初识js，qml
<!-- more -->

#### 语法

类似JS语法

##### 变量

```qml
Window {
    id: window
    // 动态绑定
    property int number: window.width
    property var obj
    // 别名
    property alias windowWidth: window.width
    // 窗口构建完成的槽
    Component.onCompleted: {

    }
}
```





#### 导入JS文件

```js
import [js文件目录] as [命名]
// example
import "./T_Js.js" as TJs
// use
new TJs.xxxx
```



#### QML基本模块

##### Window

窗口

##### Control

控制模块，可使控件

##### Item



#### 布局

1. x、y坐标，width、height大小
2. anchors属性
   - top、bottom、left、right、fill 锚点
   - margin 外边距
   - padding 内边距
3. QML Types：Flow、Grid、Row、Column
4. Qt Quick Layouts：GridLayout、RowLayout、ColumnLayout、StackLayout



#### example：

anchors:

```qml
Window {
    width: 640
    height: 480
    visible: true
    title: qsTr("Hello World")
    color: 'gray'

    Item {
        id: item1
        anchors.fill: parent
               Rectangle {
            id : rect1
            color: 'red'
            anchors.fill: parent
            anchors.margins: 100
        }
        Rectangle {
            color: 'blue'
            anchors.left: parent.left
            anchors.right: rect1.left
            anchors.top: parent.top
            anchors.bottom: rect1.top
        }
        Rectangle {
            color: 'yellow'
            anchors.left: parent.left
            anchors.right: rect1.left
            anchors.top: rect1.bottom
            anchors.bottom: parent.bottom
        }
        Rectangle {
            color: 'green'
            anchors.left: parent.left
            anchors.right: rect1.left
            anchors.top: rect1.top
            anchors.bottom: rect1.bottom
        }
    }
}
```

Flow

```qml
Item {
    id: item1
    anchors.fill: parent
    Flow {
        anchors.left: parent.left
        anchors.right: parent.right
        flow: Flow.TopToBottom

        Rectangle {
            height: 100
            width: item1.width / 2
            color: "orange"
        }
        Rectangle {
            height: 100
            width: item1.width / 2
            color: "red"
        }
        Rectangle {
            height: 100
            width: item1.width / 2
            color: "blue"
        }
    }
}
```

