---
title: qt踩坑
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 06:32:08/qt踩坑.html
mathJax: false
date: 2023-04-13 14:32:08
thumbnail:
---
记录实用Qt遇到的坑以及解决方案

<!-- more -->

##### Qt Error

###### 1. qt explicit qualification in declaration

已经存在在命名空间内，再声明函数或者类不要再加限定符，去掉函数前的`XX::`

###### 2. QT5.14.2 + MSCV2015 报错：error: C2661: “QColor::CT::CT”: 没有重载函数接受 5 个参数

解决方案

1. 保留VisualStudio2015，卸载QT5.14.2.
2. 重新下载其它QT版本，例如（5.12.9），采用MSVC2015 编译器，没有报错



##### QDialog 最小化后调用 `show()` 无法显示

使用 `showNormal()` 恢复
