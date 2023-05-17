---
title: QGC开发环境搭建
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-05-09 03:46:50/QGC开发环境搭建.html
mathJax: false
date: 2023-05-09 11:46:50
thumbnail:
---
<!-- more -->



#### 获取源码：

1. 克隆存储库（或您的分支），包括子模块：

   ```shell
   git clone --recursive -j8 https://github.com/mavlink/qgroundcontrol.git
   ```

2. 更新子模块（每次提取新源代码时都需要）：

   ```shell
   git submodule update --recursive
   ```

个别模块获取失败：libs/cpp/parse/nlohmann_json

```sh
git submodule update --init --recursive
```



#### 启动

ubuntu

```sh
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager
```





#### Q&A

qmake error : sdl2 development package not found

```shell
sudo apt install libsdl2-dev
```

