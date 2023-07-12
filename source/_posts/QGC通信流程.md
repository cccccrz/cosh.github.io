---
title: QGC通信流程
tags:
  - qt
  - QGC
categories:
  - qt
  - QGC
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-12 07:20:16/QGC通信管理.html
mathJax: false
date: 2023-07-12 15:20:16
---
> 摘要
QGC通信流程
<!-- more -->

#### **连接建立与Vehicle创建**

`LinkManager`  连接管理

`MAVLinkProtocol` MAVLink协议封装

所有的连接，通过`LinkManager` 创建管理，通过 `MAVLinkProtocol` 解析封装消息，收到心跳交给 `MultiVehicleManager`  管理；

`MultiVehicleManager` 根据情况创建 `Vehicle` 实例， `Vehicle` 直接监听全局MAVLink对象信号，获取消息，逐级处理

##### **udplink**

监听本地端口，读到数据后记录所有目标ip，端口为一个会话，视为建立连接；发送消息时会遍历所有已连接的会话，发送



#### **定时刷新连接（1s）：**

1. 自动配置的UDP连接

   `LinkManager::_addUDPAutoConnectLink` 

   `AutoConnnect.SettingsGroup.json`

2. MAVLink转发

   `LinkManager::_addMAVLinkForwardingLink`

   `App.SettingsGroup.json`  `forwardMavlink`   默认禁用



#### **启动时加载连接配置：**

`QGCApplication::_initForNormalAppBoot`

`toolbox()->linkManager()->loadLinkConfigurationList()`
