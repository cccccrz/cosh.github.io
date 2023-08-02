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

##### 1. qtcreator 卡死

![image-20230530102820530](https://cdn.jsdelivr.net/gh/cccccrz/cccccrz.github.io@main/source/img/image-20230530102820530.png)

参考[QT 5.12和有道词典冲突](https://blog.csdn.net/justformemory/article/details/107537434)

##### 2.qt翻译
由于翻译是社区贡献的，一直存在一个bug，QMessageBox翻译错误。
**快速解决：**
需要下载qt源码，将src/qttranslations/translations/qt_zh_CN.ts 中的`QDialogButtonBox` 替换为 `QPlatformTheme`
参考：
[[QTBUG-39180] QMessageBox QMessageBox::OK can not display local text - Qt Bug Tracker](https://bugreports.qt.io/browse/QTBUG-39180)
[[QTBUG-39229]上下文 QPlatformTeme 未翻译成法语、意大利语、西班牙语、葡萄牙语和其他语言 - Qt Bug Tracker](https://bugreports.qt.io/browse/QTBUG-39229)
