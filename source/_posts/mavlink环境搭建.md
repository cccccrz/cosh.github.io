---
title: mavlink环境搭建
tags:
  - mavlink
  - c++
categories:
  - mavlink
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-05-08 13:28:01/mavlink环境搭建.html
mathJax: false
date: 2023-05-08 21:28:01
thumbnail:
---


##### 本机环境：

系统：银河麒麟V10(SP1) 2203 x86_64

<!-- more -->

##### 1. 获取源码

```shell
# Dependencies
sudo apt install python3-pip

# Clone mavlink into the directory of your choice
git clone https://github.com/mavlink/mavlink.git --recursive
cd mavlink

python3 -m pip install -r pymavlink/requirements.txt
```

##### 2. 安装

```shell
cmake -Bbuild -H. -DCMAKE_INSTALL_PREFIX=install
cmake --build build --target install
```

头文件在 `install/include` 里

##### 3. 构建示例

```shell
cd examples/c
cmake -Bbuild -H. -DCMAKE_PREFIX_PATH=$(pwd)/../../install
cmake --build build
```



```shell
python3 -m pymavlink.tools.mavgen --lang=C --wire-protocol=2.0 --output=./include/ message_definitions/v1.0/common.xml
```

