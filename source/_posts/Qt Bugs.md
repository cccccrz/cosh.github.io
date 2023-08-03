---
title: Qt Bugs
tags:
  - qt
categories:
  - qt
  - bug
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-06-9 13:57:24/Qt错误日志.html
mathJax: false
date: 2023-06-9 13:57:24
thumbnail:
---


> 记录Qt的bug
> <!-- more -->

### **1. QtCreator 卡死**

可能是三方软件冲突
![image-20230530102820530](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/image-20230530102820530.png)

参考[QT 5.12和有道词典冲突](https://blog.csdn.net/justformemory/article/details/107537434)
</br>
### **2.qt 翻译 bug**
由于翻译是社区贡献的，一直存在一个bug，QMessageBox翻译错误。
**快速解决：**
需要下载qt源码，将src/qttranslations/translations/qt_zh_CN.ts 中的`QDialogButtonBox` 替换为 `QPlatformTheme`
参考：
[[QTBUG-39180] QMessageBox QMessageBox::OK can not display local text - Qt Bug Tracker](https://bugreports.qt.io/browse/QTBUG-39180)
[[QTBUG-39229]上下文 QPlatformTeme 未翻译成法语、意大利语、西班牙语、葡萄牙语和其他语言 - Qt Bug Tracker](https://bugreports.qt.io/browse/QTBUG-39229)

### **3.QtCreator 启动慢**
QtCreator 启动时会读取默认配置`QtCreator.ini` ，Windows在`%appdata%\QtProject\QtCreator.ini` ，Linux 在 `~/.config/QtProject/QtCreator.ini`。当同时启动不同版本的QtCreator时，这个配置文件会不断变大，导致再次启动时巨慢。
**快速解决：**
打开 ini 文件，里面有行 `WatchItems`，删除即可。
或
该目录有之前的配置备份，找个小的替换即可。没有可以删除，但需要重新配置Qt。