---
title: 配置Qt&Android开发环境
tags:
  - qt
  - Android
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-28 08:14:21/配置Qt&Android开发环境.html
mathJax: false
date: 2023-07-28 16:14:21
thumbnail:
---
> QtCreator 5.0以后可以自动下载；之前版本需要自行下载配置
<!-- more -->

##### **Qt 对应支持的安卓版本**

[Getting Started with Qt for Android | Qt 5.15](https://doc.qt.io/qt-5/android-getting-started.html)

|                        Qt version                        |   NDK version   |    Toolchain    |
| :------------------------------------------------------: | :-------------: | :-------------: |
| Qt 5.14.0 or later Qt 5.13.2 or later Qt 5.12.6 or later | NDK r20b or r21 | *android-clang* |
|         Qt 5.12.0 to 5.12.5 Qt 5.13.0 to 5.13.1          |    NDK r19c     | *android-clang* |
|                   Qt 5.11 and earlier                    |    NDK r10e     | *GCC* toolchain |

##### **Android sdk 版本对应 jdk 版本**

| 平台版本     | SDK版本 | 版本名称 | JDK版本 |
| ------------ | ------- | -------- | ------- |
| Android 13.0 | 33      | T        | 11      |
| Android 12.0 | 31      | S        | 11      |
| Android 11.0 | 30      | R        | 8       |
| Android 10.0 | 29      | Q        | 8       |
| Android 9.0  | 28      | Pie      | 8       |
| Android 8.1  | 27      | Oreo     | 8       |
| Android 8.0  | 26      | Oreo     | 8       |
| Android 7.1  | 25      | Nougat   | 8       |
| Android 7.0  | 24      | Nougat   | 8       |

</br>

##### **开始配置**

###### 旧版QtCreator

> QtCreator 4.10

菜单位置：工具->选项->设备->Android

1. 点击下载对应版本的 jdk，Android SDK，Android NDK，解压缩至自定义目录

   > Qt5.12.6 ，java8 ，Android SDK 25.2.5，Android NDK 20.1.5948944

2. SDK需要使用工具安装：

   1. 运行工具 `android-sdk-windows/SDK Manager.exe`

   2. 除了默认选项，勾选上 `Android SDK Build-tools` `28.0.3`版本，其他暂时没用到

3. 配置本地jdk，Android SDK，Android NDK 路径

4. 应用后自动生成套件 `Android for arm64-v8a (Clang Qt %{Qt:Version} for Android ARM64-v8a)`

###### 新版QtCreator

> 如 QtCreator 10

菜单位置：工具->外部->选项->设备->Android （新版QtCreator）

1. 点击下载对应版本的 jdk

   > Qt5.15.2  java11
   >
   > 默认下载的Android SDK版本很新，对照查询只支持java11

2. 配置本地jdk路径

3. 设置Android SDK路径(空)，点击`设置SDK`自动下载；

   或 Android SDK路径 设置已手动下载完成的路径

4. 应用后自动生成套件 `安卓 Qt %{Qt:Version} Clang Multi-Abi`

</br>

##### 运行

创建一个新项目，打开手机USB调试功能，将手机连接电脑，调试提示点击确定；

运行测试

