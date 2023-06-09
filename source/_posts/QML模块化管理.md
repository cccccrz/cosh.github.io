---
title: QML模块化管理
tags:
  - QML
  - qt
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-06-9 14:16:24/QML模块化管理.html
mathJax: false
date: 2023-06-9 14:16:24
thumbnail:
---

> Module Definition qmldir Files
>
> <!-- more -->

目录示例

![image-20230530120741110](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/image-20230530120741110.png)

##### 1.项目目录创建

src存放代码文件

src/QmlControl/ 存放QML文件，在此目录下创建QML模块，目录为模块名，每个模块下创建一个`qmldir`

##### 2.`qmldir`基本语法：

文件开头第一行声明模块名，`<ModuleIdentifier>`是模块的标识符(带点的URI符号)，它必须与模块的安装路径匹配。

后续声明文件导入URI 版本号 文件名，详见Qt文档

```qmldir
module <ModuleIdentifier> 

<ResourceIdentifier> <InitialVersion> <File>
...
```

###### example

qmldir

```qmldir
module ExampleModule
CustomButton 2.0 CustomButton20.qml
CustomButton 2.1 CustomButton21.qml
plugin examplemodule
MathFunctions 2.0 mathfuncs.js
```

qml文件使用模块

```qml
import QtQuick 2.0
import ExampleModule 2.1

Rectangle {
    width: 400
    height: 400
    color: "lightsteelblue"

    CustomButton {
        color: "gray"
        text: "Click Me!"
        onClicked: MathFunctions.generateRandom() > 10 ? color = "red" : color = "gray";
    }
}
```



##### 3.pro工程文件导入QML模块路径

此参数影响qtcreator识别模块类型，不影响编译

```pro
QML_IMPORT_PATH += $$PWD/src/QmlControl
```



##### 4.创建qrc资源文件

根目录创建`qmlcontrol.qrc`资源文件，单独管理qml，前缀该为qml导入目录 `/QmlControl`

##### 5.创建QML模块

创建qml文件`./src/QmlControl/M1/MyButton1.qml`，加入 `qmlcontrol.qrc`

开始模块管理：

1. 打开资源文件，将该文件别名改为: 模块/文件名

2. 将该模块目录下的qmldir加入资源文件，同样修改别名

3. 修改该模块下的qmldir

   ```qmldir
   Module M1
   
   MyButton 1.0 MyButton.qml
   ```

主窗口文件可以放在 `./src/QmlControl/mainWindow.qml`，QML导入路径根目录

![image-20230530120038259](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/image-20230530120038259.png)

##### 5.cpp导入QML模块路径

`QQmlApplicationEngine::addImportPath` 导入模块路径

当前模块路径为 `qrc:/QmlControl`

##### 6.cpp加载QML

`QQmlApplicationEngine::load` 加载QML文件

当前加载主窗口 `qrc:/QmlControl/mainWindow.qml`

##### 7.QML文件使用QML模块

主窗口使用

```qml
import QtQuick 2.12
import QtQuick.Window 2.12
import M1 1.0 // 导入模块

Window {
    id: mainWindow
    visible: true
    width: 640
    height: 480
    title: qsTr("Hello World")

    MyButton { // 使用模块内QML部件
    }
}
```

![image-20230530120849328](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/image-20230530120849328.png)