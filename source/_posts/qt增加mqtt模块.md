---
title: qt增加mqtt模块
tags:
  - qt
  - mqtt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-14 02:47:56/qt增加mqtt模块.html
mathJax: false
date: 2023-07-14 10:47:56
---
> qt官方提供了封装好的mqtt模块，导入即可使用；结合Qt的跨平台，很好用

<!-- more -->

emqx 写了一个qmqtt，据说掉线状态有些问题；本人倾向于使用Qt官方写的qtmqtt，且封装更到位

###  [qtmqtt](https://github.com/qt/qtmqtt)

> git 上的分支对应不同的qt版本

#### 构建

首先克隆仓库，切换至自己的qt版本分支，比如5.12.6

使用qt打开`qtmqtt.pro` 构建Release版本

##### 构建目录

构建目录会生成`bin`，`include`，`lib`，`mkspecs`

`include`是输出的头文件，引用qtmqtt/src

`mkspecs`是模块定义脚本，默认是指向构建目录

##### Windows

需要安装 perl，自动配置环境变量后，重新构建

##### Android

在windows下开发同样需要安装perl

构建步骤禁用 `Make install` 和 `Build Android APK`



#### 部署

套件目录：Qt目录/{QtVersion}/{编译器}/

##### 直接部署：

构建目录增加 `make install`， 或者命令行输入

##### 手动拷贝：

由于`build/include`是根据`src`索引的，我们在根目录下构建

`cd qtmqtt && qmake && make`

将`bin`，`include`，`lib`部署至qt安装目录，对应编译套件

将`modules-inst/qt_lib_mqtt.pri` `modules-inst/qt_lib_mqtt_private.pri`  拷贝至`套件目录/mkspecs/modules`

将`qtmqtt/src`内的头文件 拷贝至`套件目录/include`



#### 导入

```pro
QT += core netword mqtt
```

core 和 network 是两个依赖的模块，必须导入

可以打开 `qtmqtt/examples` 进行测试

