---
title: 自定义mavlink协议
tags:
  - mavlink
  - c++
categories:
  - mavlink
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-14 07:32:36/自定义mavlink协议.html
mathJax: false
date: 2023-07-14 15:32:36
---
> 本文介绍如何生成自定义mavlink协议

<!-- more -->

### 依赖

- Python 3.3+
- Python [future](http://python-future.org/)模块
- [TkInter](https://wiki.python.org/moin/TkInter) (required to use the GUI tool).
- 环境变量`PYTHONPATH`必须包含*mavlink*存储库的目录路径。

如果要创建新的XML定义，还应该安装用于XML验证和格式化的lxml和libxml2。

参考[mavlink环境搭建](https://cccccrz.github.io/2023/05/08/mavlink环境搭建/)或官方手册搭建环境。



### 自定义

`mavlink\message_definitions\v1.0` 增加自定义 XML，如`custom.xml`

在 `all.xml` 或 `common.xml` 里导入

```xml
<?xml version="1.0"?>
<mavlink>
    ...
    <!-- custom.xml range of IDs:
    messages: 18000 - 19000
    commands: 18000 - 19000
    -->
    <include>custom.xml</include>
    ...
    <messages/>
</mavlink>
```

参考枚举定义

```xml
<enum name="MAV_MODE_FLAG" bitmask="true">
    <description>These flags encode the MAV mode.</description>
    <entry value="128" name="MAV_MODE_FLAG_SAFETY_ARMED">
        <description>0b10000000 MAV safety set to armed. Motors are enabled / running / can start. Ready to fly. Additional note: this flag is to be ignore when sent in the command MAV_CMD_DO_SET_MODE and MAV_CMD_COMPONENT_ARM_DISARM shall be used instead. The flag can still be used to report the armed state.</description>
    </entry>
    <entry value="64" name="MAV_MODE_FLAG_MANUAL_INPUT_ENABLED">
        <description>0b01000000 remote control input is enabled.</description>
    </entry>
    <entry value="32" name="MAV_MODE_FLAG_HIL_ENABLED">
        <description>0b00100000 hardware in the loop simulation. All motors / actuators are blocked, but internal software is full operational.</description>
    </entry>
    <entry value="16" name="MAV_MODE_FLAG_STABILIZE_ENABLED">
        <description>0b00010000 system stabilizes electronically its attitude (and optionally position). It needs however further control inputs to move around.</description>
    </entry>
    <entry value="8" name="MAV_MODE_FLAG_GUIDED_ENABLED">
        <description>0b00001000 guided mode enabled, system flies waypoints / mission items.</description>
    </entry>
    <entry value="4" name="MAV_MODE_FLAG_AUTO_ENABLED">
        <description>0b00000100 autonomous mode enabled, system finds its own goal positions. Guided flag can be set or not, depends on the actual implementation.</description>
    </entry>
    <entry value="2" name="MAV_MODE_FLAG_TEST_ENABLED">
        <description>0b00000010 system has a test mode enabled. This flag is intended for temporary system tests and should not be used for stable implementations.</description>
    </entry>
    <entry value="1" name="MAV_MODE_FLAG_CUSTOM_MODE_ENABLED">
        <description>0b00000001 Reserved for future use.</description>
    </entry>
</enum>
```

参考心跳的Message定义

```xml
<message id="0" name="HEARTBEAT">
    <description>The heartbeat message shows that a system or component is present and responding. The type and autopilot fields (along with the message component id), allow the receiving system to treat further messages from this system appropriately (e.g. by laying out the user interface based on the autopilot). This microservice is documented at https://mavlink.io/en/services/heartbeat.html</description>
    <field type="uint8_t" name="type" enum="MAV_TYPE">Vehicle or component type. For a flight controller component the vehicle type (quadrotor, helicopter, etc.). For other components the component type (e.g. camera, gimbal, etc.). This should be used in preference to component id for identifying the component type.</field>
    <field type="uint8_t" name="autopilot" enum="MAV_AUTOPILOT">Autopilot type / class. Use MAV_AUTOPILOT_INVALID for components that are not flight controllers.</field>
    <field type="uint8_t" name="base_mode" enum="MAV_MODE_FLAG" display="bitmask">System mode bitmap.</field>
    <field type="uint32_t" name="custom_mode">A bitfield for use for autopilot-specific flags</field>
    <field type="uint8_t" name="system_status" enum="MAV_STATE">System status flag.</field>
    <field type="uint8_t_mavlink_version" name="mavlink_version">MAVLink version, not writable by user, gets added by protocol because of magic data type: uint8_t_mavlink_version</field>
</message>
```

开始自定义协议`custom.xml`

```xml
<?xml version="1.0"?>
<mavlink>
  <version>3</version>	
	<!--  custom message 18xxx -->
    <message id="18001" name="CUSTOM_DEFINE_1">
      <description>custom description 1</description>
      <field type="uint8_t[10]" name="targetId">description1</field>
      <field type="uint8_t[10]" name="targetType">description2</field>
      <field type="uint16_t[10]" name="xMin"> description3</field>
      <field type="uint16_t[10]" name="yMin"> description4</field>
      <field type="uint16_t[10]" name="xMax"> description5</field>
      <field type="uint16_t[10]" name="yMax"> description6</field>
    </message>
    <message id="18002" name="CUSTOM_DEFINE_2">
      <description>custom description 2</description>
      <field type="uint8_t" name="cmdType">start from 1,0 is null</field>
      <field type="uint8_t" name="targetId">start from 1,0 is to custom rectangle area</field>
      <field type="uint16_t" name="xMin"> target pixel left top x</field>
      <field type="uint16_t" name="yMin"> target pixel left top y</field>
      <field type="uint16_t" name="xMax"> target pixel right bottom x</field>
      <field type="uint16_t" name="yMax"> target pixel right bottom y</field>
    </message>
    ...
  </messages>
</mavlink>
```



### 生成

```sh
python3 -m mavgenerate
```

XML 选择`qll.xml` 全部生成

Out 输出目录自定

Language，Protocol 自选，这里选c++11，2.0

点击Generate生成头文件

自行导入并使用


参考文章:  
[Generate MAVLink Libraries · MAVLink Developer Guide](https://mavlink.io/zh/getting_started/generate_libraries.html)
