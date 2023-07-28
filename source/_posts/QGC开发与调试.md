---
title: QGC开发与调试
tags:
  - qt
  - QGC
  - QML
categories:
  - qt
  - QGC
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-28 08:31:36/QGC开发与调试.html
mathJax: false
date: 2023-07-28 16:31:36
thumbnail:
---

> 使用PX4进行仿真
>
> <!-- more -->

### 代码风格

使用QGC官方代码风格，参考：[Coding Style · QGroundControl Developer Guide](https://dev.qgroundcontrol.com/master/en/contribute/coding_style.html)

使用`Doxygen`注释规范

</br>

### 模块化管理

##### QML

详见个人博客：[QML模块化管理](https://cccccrz.github.io/2023/06/09/QML模块化管理/)

</br>

### 仿真

1. 获取PX4源码

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git --recursive
   ```

2. 工具链安装脚本

   ```sh
   bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
   ```

3. 工具链可能的依赖

   ```sh
   # jMAVSim
   sudo apt-get install cmake genromfs ninja-build exiftool vim-common -y
   # python
   sudo apt-get install python-argparse python-numpy python-yaml python3-dev python3-pip -y
   sudo -H pip install --upgrade pip 
   sudo -H pip install pandas jinja2 pyserial cerberus
   # maybe need
   sudo apt-get install protobuf-compiler libeigen3-dev libopencv-dev -y
   ```

4. jMAVSim 仿真飞行

   ```sh
   make px4_sitl_default jmavsim
   ```

5. Gazebo Classic Vehicles 仿真

   ```sh
   make px4_sitl gazebo-classic
   ```

   支持视频流仿真

   ```sh
   make px4_sitl gazebo-classic_typhoon_h480
   ```

6. 多机仿真

   [Multi-Vehicle Simulation with Gazebo Classic | PX4 自动驾驶用户指南](https://docs.px4.io/main/zh/sim_gazebo_classic/multi_vehicle_simulation_gazebo.html)
