---
title: QGC开发环境搭建
tags:
  - qt
  - QGC
categories:
  - qt
  - QGC
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

出现 the untracked or modified content in submodules

进入该子模块目录，清理

```bash
git reset --hard HEAD
git clean -fxd
```

或者

```bash
git submodule foreach git reset --hard
```

参考：[git submodules with modified and untracked content - why and how to remove it? - Stack Overflow](https://stackoverflow.com/questions/7993413/git-submodules-with-modified-and-untracked-content-why-and-how-to-remove-it/28322320)



#### 启动

ubuntu

```sh
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager
```





#### Q&A

1.qmake error : sdl2 development package not found

```shell
sudo apt install libsdl2-dev
```



2.Linux 安装GStreamer后编译报错：g_once_init_enter ` ‘__atomic_load’ must not be a pointer to a ‘volatile’ type`

解决方案：

删除错误处的 volatile 声明

相关链接：[GCC 11 的构建错误（原子作为易失性和循环构造） ·问题 #9669 ·马夫林克/Q地面控制 ·GitHub](https://github.com/mavlink/qgroundcontrol/issues/9669)



