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
---
> 源码获取&&模块安装&&问题解决
>
> 参考[QGC开发手册](https://dev.qgroundcontrol.com/master/en/getting_started/)
>
> <!-- more -->

### 获取源码：

克隆存储库（或您的分支），包括子模块：

```shell
git clone --recursive -j8 https://github.com/mavlink/qgroundcontrol.git
```

更新子模块（每次提取新源代码时都需要）：

```shell
git submodule update --recursive
```

个别模块获取失败：libs/cpp/parse/nlohmann_json

```sh
git submodule update --init --recursive
```

出现 `the untracked or modified content in submodules`

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

</br>

### 配置开发环境

##### 开发环境(maybe QGC 4.0):

- **Windows:** Vista or higher, [Visual Studio 2017 compiler](https://dev.qgroundcontrol.com/master/en/getting_started/#vs) (64 bit)
- **Android**: Jelly Bean (4.1) and higher.
  - Standard QGC is built against ndk version 19.
  - Java JDK 8 is required.
- **Qt version:**  t5.12 ~ 5.14

##### 开发环境(QGC 4.2.8)：

- **macOS:** v10.11 or higher
- **Ubuntu:** 64 bit, gcc compiler
- **Windows:** Vista or higher, [Visual Studio 2019 compiler](https://dev.qgroundcontrol.com/master/en/getting_started/#vs) (64 bit)
- **iOS:** 10.0 and higher
- **Android**: Jelly Bean (4.1) and higher.
  - Standard QGC is built against ndk version 19.
  - Java JDK 11 is required.
- **Qt version:** 5.15.2 **(only)**

##### 依赖

- **Ubuntu:** `sudo apt-get install speech-dispatcher libudev-dev libsdl2-dev patchelf build-essential curl libxcb-xinerama0 -y`
- **Fedora:** `sudo dnf install speech-dispatcher SDL2-devel SDL2 systemd-devel patchelf`
- **Arch Linux:** `pacman -Sy speech-dispatcher patchelf`
- **Android:** [Qt Android Setup](http://doc.qt.io/qt-5/androidgs.html)  ；个人博客：配置Qt&Android开发环境

</br>

### 安装可选模块

> 相关配置在`QGCExternalLibs.pri`  ，工程文件会检测是否安装进行条件编译

##### **gstreamer**

该模块提供视频流支持；

需要克隆 `libs\qmlglsink\gst-plugins-good` ，并下载 gstreamer 库

*Windows/Android*

下载地址：[Download GStreamer](https://gstreamer.freedesktop.org/download/)

*Linux*

```sh
sudo apt-get install libgstreamer* -y
```

</br>

##### **AirMap**

仅Linux平台支持，可使用配置文件禁用

```sh
echo -e "DEFINES += DISABLE_AIRMAP\r\n" | tee user_config.pri
```

相关配置在`QGCExternalLibs.pri` `[OPTIONAL] AirMap Support`

自动下载失败可以手动下载，目录在 `{构建目录}/libs/airmap-platform-sdk`；可以使用迅雷下载后拷贝至该目录

```sh
sudo apt install curl
curl --location --output ./airmap-platform-sdk.deb https://github.com/airmap/platform-sdk/releases/download/2.0.1/airmap-platform-sdk-2.0.1-Linux-x86_64.deb
```

解压至tmp

```sh
ar p airmap-platform-sdk.deb data.tar.gz | tar xvz -C ./tmp/ --strip-components=1
```

拷贝

```sh
mv -u ./tmp/usr/lib/x86_64-linux-gnu/* ./linux/Qt.5.15/
mv -u ./tmp/usr/include/airmap/* ./include/airmap/
rm -rf ./tmp
rm ./airmap-platform-sdk.deb
```

</br>

### 最初的启动

ubuntu 需要执行如下命令

```sh
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager
```

</br>

### 配置仿真环境

> 详见个人博客： QGC开发与调试

查阅PX4手册>开发>仿真

视频，模拟飞行；已搭建

多机控制；待搭建

</br>

### Q&A

1.qmake error : sdl2 development package not found

```shell
sudo apt install libsdl2-dev
```



2.Linux 安装GStreamer后编译报错：g_once_init_enter ` ‘__atomic_load’ must not be a pointer to a ‘volatile’ type`

解决方案：

删除错误处的 volatile 声明

相关链接：[GCC 11 的构建错误（原子作为易失性和循环构造） ·问题 #9669 ·马夫林克/Q地面控制 ·GitHub](https://github.com/mavlink/qgroundcontrol/issues/9669)



