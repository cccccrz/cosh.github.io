---
title: QGC架构设计
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-05-10 10:56:24/QGC架构.html
mathJax: false
date: 2023-05-10 18:56:24
thumbnail:
---
[TOC]

<!-- more -->

#  用户界面设计

QGC中UI设计的主要模式是用QML编写的UI页面，多次与用C ++编写的自定义“Controller”进行通信。 这种设计模式有点沿用MVC设计模式，但也有显著不同之处。

QML代码通过以下机制绑定到与系统关联的信息：

- 自定义控制器
- 全局QGroundControl对象，提供对活动Vehicle等内容的访问
- FactSystem提供对参数的访问，在某些情况下提供自定义事实。

注意：由于QGC中使用的QML的复杂性以及它依赖于与C ++对象的通信来驱动ui，因此无法使用Qt提供的QML Designer来编辑QML。

## 多设备设计模式

QGroundControl设计用于从桌面到笔记本电脑，平板电脑和使用鼠标和触摸的小型手机屏幕等多种设备类型。 以下是QGC如何做到以及背后原理的描述。

### 目标设备

从触摸的角度和屏幕尺寸的角度来看，QGC UI设计的优先目标是平板电脑（比如三星Galaxy）。 由于此决定，其他设备类型和大小可能会看到一些视觉和/或可用性的错误。 基于优先级的决策时的当前顺序是平板电脑，笔记本电脑，台式机，电话（任何小屏幕）。



### 使用的开发工具

#### Qt布局控件

QGC没有针对不同屏幕尺寸和/或形状因子的不同编码的UI。 通常，它使用QML Layout功能来重排一组QML UI代码以适应不同的外形。 在某些情况下，它提供了较小的屏幕尺寸细节，使事情适合。 但这是一个简单的可见性模式。

#### FactSystem（事实系统）

QGC内部是一个系统，用于管理系统中的所有单个数据。 这个数据模型是连接到控件的。

#### 严重依赖可重用控件

QGC UI是从一组可重用的控件和UI元素开发而来的。 这样，现在可以在整个UI中使用添加到可重用控件的任何新功能。 这些可重用的控件还连接到FactSystem Facts，然后FactSystem Facts自动提供适当的UI。



## 字体和调色板

QGC有一套标准的字体和调色板，应该由所有用户界面使用。

```qml
import QGroundControl.Palette       1.0
import QGroundControl.ScreenTools   1.0
```

### QGCPalette(QGC调色板)

此项目显示QGC调色板。 这个调色板有两种变体：浅色和深色。 较浅的调色板适合户外使用，黑色调色板适用于室内。 通常，您不应该直接为UI指定颜色，您应该使用调色板中的颜色。 如果不遵循此规则，则您创建的用户界面将无法通过浅色/深色样式更改。

### QGCMapPalette(QGC地图调色板)

地图调色板用于绘制地图的颜色。 由于不同的地图样式，特别是卫星和街道，您需要有不同的颜色来清晰地绘制它们。 卫星地图需要更浅的颜色，而街道地图需要更深的颜色。 QGCMapPalette项目为此提供了一组颜色，以及在地图上切换浅色和深色的功能。

### ScreenTools (屏幕工具)

ScreenTools项提供可用于指定字体大小的值。 它还提供有关屏幕大小以及QGC是否在移动设备上运行的信息。



## 用户界面控件

QGC提供了一组用于构建用户界面的基本控件。 一般来说，它们往往是Qt支持的基本QML控件上方的薄层，Qt控件支持QGC调色板。

```
import QGroundControl.Controls 1.0
```

### Qt控件

以下控件是标准Qt QML控件的QGC变体。 除了使用QGC调色板绘制。 它还们提供与相应Qt控件相同的功能，

- QGCButton
- QGCCheckBox
- QGColoredImage
- QGCComboBox
- QGC可轻弹
- QGCLabel
- QGCM可操作项
- QGCRadioButton
- QGCSlider
- QGCTextField

### QGC 控件

这些自定义控件是QGC独有的，用于创建标准UI元素。

- DropButton - RoundButton，单击时会删除一组选项。 示例是平面视图中的同步按钮。
- ExclusiveGroupItem - 用于支持QML ExclusiveGroup 概念的自定义控制的基础项目。
- QGCView - 系统中所有顶级视图的基本控件。 提供对FactPanels的支持并显示QGCViewDialogs和QGCViewMessages。
- QGC View对话框 - 从QGC视图右侧弹出的对话框。 您可以指定对话框的接受/拒绝按钮以及对话框内容。 使用示例是当您单击某个参数并显示值编辑器对话框时。
- QGCViewMessage - QGCViewDialog的简化版本，允许您指定按钮和简单的文本消息。
- QGCViewPanel - QGCView内部的主要视图内容。
- RoundButton - 一个圆形按钮控件，它使用图像作为其内部内容。
- SetupPage - 所有安装载具组件页面的基本控件。 提供标题，说明和组件页面内容区域。



# Fact System(事实系统)

Fact System(事实系统)提供一组标准化和简化QGC用户界面创建的功能。

## Fact

事实代表系统中的单个值。

## FactMetaData

与每个事实有FactMetaData相关联 它提供有关事实的详细信息，以便驱动自动用户界面生成和验证。

## 事实控制

事实控件是一个QML用户界面控件，它连接到Fact和它的FactMetaData，为用户提供控件以修改/显示与Fact相关的值。

## FactGroup（事实小组）

事实*组*是一组[事实](https://dev.qgroundcontrol.com/master/zh/fact_system.html#fact)。它用于组织事实和管理用户定义的事实。

## 自定义构建支持

用户定义的事实可以通过覆盖自定义固件插件类中的函数来添加。这些函数返回用于标识添加的事实组的名称到事实组映射。可以通过扩展类来添加自定义事实数据组。FactMetaDatas可以通过提供包含必要信息的json文件来使用适当的构造函数来定义。`factGroups``FirmwarePlugin``FactGroup``FactGroup`

也可以通过覆盖类来更改现有事实的元数据。`adjustMetaData``FirmwarePlugin`

可以使用或访问与车辆相关的事实（包括属于车辆固件插件返回的事实组的事实）`factGroups``getFact("factName")``getFact("factGroupName.factName")`

有关其他信息，请参阅[固件插件中的](https://github.com/mavlink/qgroundcontrol/blob/v4.0.8/src/FirmwarePlugin/FirmwarePlugin.h)注释。



# 顶级视图

本节包含有关顶级视图代码的主题：设置，设置，计划和飞行。

## Settings View

- 顶级QML代码是AppSettings.qml
- 每个按钮都会加载一个单独的QML页面

##  Setup View

- 在SetupView.qml中实现的顶级QML代码
- 固定的按钮/页面集：摘要，固件
- 按钮/页面的剩余部分来自AutoPilot Plugin Vehicle Component列表

## Plan View

- 最高级别的 QML 代码是 [PlanView.qml](https://github.com/mavlink/qgroundcontrol/blob/master/src/PlanView/PlanView.qml)
- 主视图UI是FlightMap控件
- QML与MissionController（C ++）通信，后者为视图提供任务项数据和方法

### 用于任务项目编辑的动态UI

#### 任务指令树

QGC 创建用户界面，用于从 json 元数据的层次结构中动态编辑特定任务项命令。 此层次结构称为任务命令树。 这样，在添加新命令时，只能创建 json 元数据。

##### 为什么是一颗树？

需要该树以不同的方式处理不同固件和／或不同的车辆类型，以支持不同的命令。 最简单的例子是 mavlink 规范可能包含了并非所有固件都支持的命令参数。 或着命令参数仅对某些车辆类型有效。 此外，在某些情况下，GCS 可能会决定将某些命令参数在视图中对最终用户进行隐藏，因为它们过于复杂或导致可用性问题。

该树是MissionCommandTree类： [MissionCommandTree.cc](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MissionCommandTree.cc), [MissionCommandTree.h](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MissionCommandTree.h)

##### 树根目录

树的根目录是与 mavlink 规范完全匹配的json元数据。

您可以在这里看到`MAV_CMD_NAV_WAYPOINT`根目录[json](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoCommon.json#L27)的示例：

```
        {
            "id":                   16,
            "rawName":              "MAV_CMD_NAV_WAYPOINT",
            "friendlyName":         "Waypoint",
            "description":          "Travel to a position in 3D space.",
            "specifiesCoordinate":  true,
            "friendlyEdit":         true,
            "category":             "Basic",
            "param1": {
                "label":            "Hold",
                "units":            "secs",
                "default":          0,
                "decimalPlaces":    0
            },
            "param2": {
                "label":            "Acceptance",
                "units":            "m",
                "default":          3,
                "decimalPlaces":    2
            },
            "param3": {
                "label":            "PassThru",
                "units":            "m",
                "default":          0,
                "decimalPlaces":    2
            },
            "param4": {
                "label":            "Yaw",
                "units":            "deg",
                "nanUnchanged":     true,
                "default":          null,
                "decimalPlaces":    2
            }
        },
```

注意：在现实中，基于此的信息应由 mavlink 本身提供，而不需要成为 GCS 的一部分。

##### 叶节点

然后，叶节点提供元数据，这些元数据可以覆盖命令的值和/或从显示给用户的参数中删除参数。 完整的树层次结构是这样的：

- 根－通用Mavlink
  - 特定的车辆类型－特定于车辆的通用规范
  - 特定的硬件类型－每个固件类型有一个可选的叶节点（PX4/ArduPilot）
    - 特定的车辆类型－每个车辆类型有一个可选的叶节点（FW/MR/VTOL/Rover/Sub）

注意：实际上，此替代功能应该是mavlink规格的一部分，并且应该可以从车辆中查询。

##### 从完整树中构建实例树

由于 json 元数据提供了所有固件／车辆类型组合的信息，因此必须根据用于创建计划的固件和车辆类型来构建要使用的实际树。 这是通过一个进程调用“collapsing”的完整树到一个固件／车辆的特定树来完成的 ([code](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MissionCommandTree.cc#L119))。

步骤如下：

- 在实例树种添加根
- 将特定的车辆类型重写实例树
- Apply the firmware type specific overrides to the instance tree
- 将特定的硬件／车辆类型重写实例树

然后，生成的任务命令树将为平面项目编辑器构建UI。 实际上，它不仅用于此，还有许多其他地方可以帮助您了解有关特定命令 id 的更多信息。

#### 层次结构示例 `MAV_CMD_NAV_WAYPOINT`

让我们来看看`MAV_CMD_NAV_WAYPOINT`的示例层次结构。 根信息如上图所示。

##### 根－车辆类型的特定叶节点

层次结构的下一个层级是通用的 mavlink，但只针对特定的车辆。 这里的Json文件：[MR](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoMultiRotor.json), [FW](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoFixedWing.json), [ROVER](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoRover.json), [Sub](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoSub.json), [VTOL](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MavCmdInfoVTOL.json)。 这个是重写（固定翼）　

```
        {
            "id":           16,
            "comment":      "MAV_CMD_NAV_WAYPOINT",
            "paramRemove":  "4"
        },
```

这样做是删除参数4的编辑 UI，固定翼没有使用航向（Yaw）参数。 由于这是根的叶节点，因此无论固件类型如何，这都适用于所有固定翼车辆。

##### 根－硬件类型的特定叶节点

层次结构的下一层级是特定于固件类型但适用于所有车辆类型的替代。 再次让我们看看航点（Waypoint）覆盖：

[ArduPilot](https://github.com/mavlink/qgroundcontrol/blob/master/src/FirmwarePlugin/APM/MavCmdInfoCommon.json#L6)：

```
        {
            "id":           16,
            "comment":      "MAV_CMD_NAV_WAYPOINT",
            "paramRemove":  "2"
        },
```

[PX4](https://github.com/mavlink/qgroundcontrol/blob/master/src/FirmwarePlugin/PX4/MavCmdInfoCommon.json#L7)：

```
        {
            "id":           16,
            "comment":      "MAV_CMD_NAV_WAYPOINT",
            "paramRemove":  "2,3"
        },
```

您可以看到，对于两个固件参数参数2，即接受半径，从编辑 ui 中删除。 这是QGC的特性决定。 与指定值相比，使用固件通用接受半径会更加安全和容易。 因此，我们决定对用户隐藏它。

您还可以看到，对于 PX4 param3/PassThru，由于 PX 不支持它，因此已被删除。

##### 根－特定于固件的类型－特定于车辆类型的叶子节点

层次结构的最后一个级别既针对固件又针对车辆类型。

[ArduPilot/MR](https://github.com/mavlink/qgroundcontrol/blob/master/src/FirmwarePlugin/APM/MavCmdInfoMultiRotor.json#L7):

```
        {
            "id":           16,
            "comment":      "MAV_CMD_NAV_WAYPOINT",
            "paramRemove":  "2,3,4"
        },
```

在这里你可以看到，ArduPilot的多电机车辆参数2/3/4 Acceptance/PassThru/Yaw 已被移除。 例如，航向（Yaw）是因为不支持所以被移除。 由于此代码的工作原理的怪癖，您需要从较低级别重复重写。

#### 任务命令 UI 信息

两个类定义与命令相关联的元数据：

- MissionCommandUIInfo－整个命令的元数据
- MissionCmdParamInfo－命令中参数的元数据

源中注释了支持 json 键的完整详细信息。

[MissionCommandUIInfo](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MissionCommandUIInfo.h#L82)：

```
/// 与任务命令关联的 UI 信息 （MAV_CMD）
///
///MissionCommandUIInfo用于自动为MAV_CMD生成编辑ui。 此对象还支持仅具有一组命
/// 令的部分信息的概念。 这用于创建基本命令信息的替代。 对于覆盖，只需从基本命
/// 令 ui 信息中指定要修改的键即可。 若要重写 ui 参数信息，必须指定整个MissionParamInfo对象。
///
/// MissionCommandUIInfo对象的json格式为：
///
/// 键值                   　类型    默认值     描述
/// id                      int     reauired    MAV_CMD id
/// comment                 string              用于添加评论
/// rawName                 string  required    MAV_CMD 枚举名称，仅应设置基础树信息
/// friendlyName            string  rawName     命令的简单描述
/// description             string              命令的详细描述
/// specifiesCoordinate     bool    false       true: 命令指定一个纬／经／高坐标
/// specifiesAltitudeOnly   bool    false       true: 命令仅指定高度（非坐标）
/// standaloneCoordinate    bool    false       true: 车辆无法通过与命令关联的坐标（例如：ROI）
/// isLandCommand           bool    false       true: 命令指定着陆指令 (LAND, VTOL_LAND, ...)
/// friendlyEdit            bool    false       true: 命令支持友好的编辑对话框，false：Command仅支持“显示所有值”样式的编辑
/// category                string  Advanced    该命令所属的类别
/// paramRemove             string              由替代使用以删除参数，例如：“ 1,3”将删除替代上的参数1和3
/// param[1-7]              object              MissionCommandParamInfo 对象
///
```

[MissionCmdParamInfo](https://github.com/mavlink/qgroundcontrol/blob/master/src/MissionManager/MissionCommandUIInfo.h#L25)：

```
/// 与任务命令 （MAV_CMD） 参数关联的 UI 信息
///
/// MissionCommandParamInfo 用于自动为与 MAV_CMD 关联的参数生成编辑ui。
///
/// MissionCmdParamInfo 对象的 Json 文件格式为：
///　
/// 键值             类型    默认值     描述
/// label           string  required    文本字段标签
/// units           string              值的单位，应使用 FactMetaData Units 字符串以获得自动转换
/// default         double  0.0/NaN     默认参数值。 如果未指定默认值且 nanunchange==true，默认值为NaN。
/// decimalPlaces   int     7           显示值得小数位数
/// enumStrings     string              要在组合框中显示以供选择的字符串
/// enumValues      string              与每个枚举字符串关联的值
/// nanUnchanged    bool    false       True: 值可以设置为NaN表示信号不变
```



##  Fly View

- 顶级QML代码在FlightDisplayView.qml中
- QML代码与MissionController（C ++）通信以进行任务显示
- 仪器小部件与活动的Vehicle对象通信
- 两个主要的内部view是：
  - `FlightDisplayViewMap`
  - `FlightDisplayViewVideo`



# 文件格式

本节包含有关QGroundControl使用/支持的文件格式的主题。

## 开发者工具

QGroundControl主要为自动驾驶开发人员提供了许多工具。 这些简化了常见的开发人员任务，包括设置模拟连接以进行测试，以及通过MAVLink访问系统Shell。

> 注意:在调试模式下构建源以启用这些工具。

工具包括：

- 模拟链接（仅限每日构建） - 创建和停止多个模拟载具链接。
- 重播飞行数据 - 重播遥测日志（用户指南）。
- MAVLink Inspector - 显示收到的MAVLink消息/值。
- MAVLink分析器 - 绘制MAVLink消息/值的趋势图。
- 自定义命令小组件 - 在运行时加载自定义/测试QML UI。
- 板载文件 - 导航车辆文件系统和上载/下载文件。
- HIL Config Widget - HIL模拟器的设置.
- MAVLink控制台（仅限PX4） - 连接到PX4 nsh shell并发送命令。

### 模拟链接

Mock Link允许您在QGroundControl调试版本中创建和停止指向多个模拟（模拟）载具的链接。

模拟不支持飞行，但允许轻松测试：

- 任务上传/下载
- 查看和更改参数
- 测试大多数设置页面
- 多个载具用户界面

对于任务上载/下载的单元测试错误情况尤其有用。

### 使用Mock Link

为了使用Mock Link：

1. 1.通过构建源来创建调试版本。

2. 通过选择顶部工具栏中的“应用程序设置”图标，然后选择侧栏中的“模拟链接”来访问“模拟链接”：

   ![img](https://dev.qgroundcontrol.com/master/assets/tools/mocklink_waiting_for_connection.jpg)

3. 1. 可以单击面板中的按钮以创建相关类型的车辆链接。

4. 每次单击按钮时，都会创建一个新连接。

5. 当存在多个连接时，将显示多车辆UI。

   ![img](https://dev.qgroundcontrol.com/master/assets/tools/mocklink_connected.jpg)

6. 1. 单击停止一个模拟链接以停止当前活动的车辆。

然后使用模拟链接或多或少与使用任何其他载具相同，只是模拟不允许飞行。



# 命令行选项

您可以使用命令行选项启动QGroundControl。 这些用于启用日志记录，运行单元测试以及模拟不同的主机环境以进行测试。

## 使用选项启动QGroundControl

您需要打开命令提示符或终端，将目录更改为存储qgroundcontrol.exe的位置，然后运行它。 每个平台如下所示（使用--logging：full选项）：

Windows命令提示符：

```bash
cd "\Program Files (x86)\qgroundcontrol"
qgroundcontrol --logging:full
```

OSX终端应用程序（应用程序/实用程序）：

```bash
cd /Applications/qgroundcontrol.app/Contents/MacOS/
./qgroundcontrol --logging:full
```

Linux终端：

```bash
./qgroundcontrol-start.sh --logging:full
```

## 选项

选项/命令行参数列在下表中。

| 选项 | 描述 |
| ---- | ---- |
| `    |      |

clear-settings`| 清除应用程序设置（将QGroundControl恢复为默认设置）。 | |`logging:full`| 打开完整日志记录。 请参阅控制台日志记录 | |`logging:full,LinkManagerVerboseLog,ParameterLoaderLog`| 打开完整日志记录并关闭以下列出的以逗号分隔的日志记录选项。 | |`--llogging:LinkManagerLog,ParameterLoaderLog`| 打开指定的逗号分隔日志记录选项 | |`--unittest:name`| （仅限Debug构建）运行指定单元测试。 离开：运行所有测试的名称。 | |`--unittest-stress:name`| （仅限调试版本）连续运行指定的单元测试20次。 离开：运行所有测试的名称。 | |`-fake-mobile`| 模拟在移动设备上运行。 | |`--test-high-dpi` | 模拟在高DPI设备上运行QGroundControl。 |

笔记：

- 单元测试自动包含在调试版本中（作为QGroundControl的一部分）。 QGroundControl在单元测试的控制下运行（它不能正常启动）。





# 自定义构建

自定义构建允许第三方创建自己的QGC版本，使其能够轻松跟上常规QGC中所做的更改。QGC内置了一个架构，允许自定义构建修改并添加到常规QGC的功能集中。

定制构建的一些可能性

- 为您的构建打造完全品牌化
- 定义单个外部测试版堆栈以避免携带不必要的代码
- 实现您自己的、自动驾驶仪和固件插件覆盖
- 实现您自己的相机管理器和插件覆盖
- 实现您自己的QtQuick接口模块
- 实现您自己的工具栏、工具栏指示器和 UI 导航
- 实现您自己的飞行视图叠加层（以及如何隐藏QGC中的元素，例如飞行小部件）
- 实现您自己的自定义QtQuick相机控制
- 实施您自己的自定义飞行前检查清单
- 为上述所有内容定义您自己的资源

QGC为任何支持mavlink的车辆提供通用支持，并为PX4 Pro和ArduPilot提供固件特定支持的缺点之一是用户界面的复杂性。由于QGC事先不知道有关您的车辆的任何信息，因此需要UI位，如果您驾驶的车辆仅使用PX4 Pro固件并且是多旋翼车辆，则可能会妨碍您。如果这是已知的事情，那么可以在不同的地方简化UI。此外，QGC既针对从头开始构建自己车辆的DIY用户，也针对现成车辆的商业用户。从头开始设置 DIY 无人机需要各种功能，而这些功能对于现成车辆的用户来说是不需要的。因此，对于现成的车辆用户来说，所有 DIY 特定的东西都只是他们需要查看的额外噪音。创建自定义构建允许您为车辆指定确切的详细信息并隐藏不相关的内容，从而为您的用户创建比常规通用 QGC 更简单的用户体验。

QGC 中有一个插件架构，允许这种自定义构建创建。它们可以在QGCCorePlugin.h，FirmwarePlugin.h和AutoPilotPlugin.h相关类中找到。要创建自定义构建，您需要创建标准插件的子类，覆盖适合您使用的方法集。

还有一种机制允许您覆盖资源，以便您可以更改 QGC 中较小的可视元素。

QGC内部还有“高级模式”的概念。而标准 QGC 构建始终在高级模式下运行。自定义构建始终以常规/非高级模式启动。构建中有一种更简单的机制可以打开高级模式，即相当快地连续单击 5 次飞行视图按钮。如果在自定义版本中执行此操作，则会警告您进入高级模式。这里的概念是隐藏普通用户不应该在高级模式后面访问的内容。例如，商用车将不需要访问大多数面向DIY设置的设置页面。因此，自定义构建可以隐藏这一点。自定义示例代码演示如何执行此操作。

如果您想了解可能性，第一步是通读那些记录可能性的文件。接下来查看 [

```
custom-example](https://github.com/mavlink/qgroundcontrol/tree/master/custom-example) source code including the 
```

[自述文件](https://github.com/mavlink/qgroundcontrol/blob/master/custom-example/README.md)。

## 自定义构建的初始存储库设置

在QGC和QGC的自定义构建版本上工作的建议机制是有两个单独的存储库。第一个存储库是你的主 QGC 分叉。第二个存储库是自定义生成存储库。

### 主要QGC存储库

此存储库用于处理对主线 QGC 的更改。在创建自己的自定义构建时，发现您可能需要对自定义构建进行调整/添加以实现所需的内容的情况并不少见。通过与 QGC 开发人员直接讨论这些需要的更改并提交拉取以使自定义构建架构更好，您可以使 QGC 对每个人来说更强大，并回馈社区。

创建此存储库的最佳方法是将常规 QGC 存储库分叉到您自己的 GitHub 帐户。

### 自定义构建存储库

这是您将进行主要自定义构建开发的地方。此处的所有更改都应在自定义目录中，而不是渗入常规的QGC代码库。

由于只能分叉一次存储库，因此创建此存储库的方法是在 GitHub 帐户中“创建新存储库”。不要向其添加任何其他文件，如 gitignore、readme 等。创建后，您可以选择设置存储库。现在您可以选择“从另一个存储库导入代码”。只需使用“导入代码”按钮导入常规 QGC 存储库即可。

## 自定义构建插件

为自定义构建自定义 QGC 的机制是通过现有的 和体系结构。通过在自定义构建中创建这些插件的子类，您可以更改 QGC 的行为以满足您的需求，而无需修改上游代码。`FirmwarePlugin``AutoPilotPlugin``QGCCorePlugin`

### QGCCorePlugin

这允许您修改 QGC 中与车辆没有直接关系但与 QGC 应用程序本身相关的部分。这包括应用程序设置、调色板、品牌等内容。



## 资源覆盖

QGC 源代码术语中的“资源”是指[在 qgroundcontrol.qrc](https://github.com/mavlink/qgroundcontrol/blob/master/qgroundcontrol.qrc) 和 [qgcresources.qrc](https://github.com/mavlink/qgroundcontrol/blob/master/qgcresources.qrc) 文件中找到的任何内容。通过覆盖资源，您可以将其替换为您自己的版本。这可以像单个图标一样简单，也可以像替换 qml UI 代码的整个车辆设置页面一样复杂。

请注意，使用资源覆盖并不像插件架构那样将您与上游 QGC 更改隔离开来。从某种意义上说，您直接修改了主代码使用的上游 QGC 资源。

### 排除文件

覆盖资源的第一步是将其从上游构建的标准部分中“排除”。这意味着您将在自己的自定义生成资源文件中提供该资源。有两个文件可以实现这一点：[qgroundcontrol.exclusion和qgcresources.exclusion](https://dev.qgroundcontrol.com/master/zh/custom_build/ResourceOverride.html)。它们直接与 *.qrc 对应项对应。要排除资源，请将资源行从 .qrc 文件复制到相应的 .exclusion 文件中。

### 排除资源的自定义版本

必须在自定义生成资源文件中包含重写资源的自定义版本。资源别名必须与上游别名完全匹配。资源的名称和实际位置可以位于自定义目录结构中的任何位置。

### 生成标准 QGC 资源文件的新修改版本

这是使用 python 脚本完成的。它将读取上游和文件以及相应的排除文件，并在自定义目录中输出这些文件的新版本。这些新版本将没有您指定要排除的资源。自定义生成的构建系统使用这些生成的文件（如果存在）而不是上游版本进行构建。这些文件的生成版本应添加到存储库中。此外，每当在自定义存储库中更新 QGC 的上游部分时，都必须重新运行以生成文件的新版本，因为上游资源可能已更改。`updateqrc.py``qgroundcontrol.qrc``qgcresources.qrc``python updateqrc.py`

### 自定义构建示例

可以在存储库自定义构建示例中查看自定义生成 qgcresource 覆盖的示例：

- [qgcresources.qrc](https://github.com/mavlink/qgroundcontrol/blob/master/custom-example/qgcresources.exclusion)
- [custom.qrc](https://github.com/mavlink/qgroundcontrol/blob/master/custom-example/custom.qrc)

## 定制

以下主题说明如何自定义 UI 的各种视图和其他部分。

- [首次运行提示](https://dev.qgroundcontrol.com/master/zh/custom_build/FirstRunPrompts.html)
- [工具栏自定义](https://dev.qgroundcontrol.com/master/zh/custom_build/Toolbar.html)
- [飞视图定制](https://dev.qgroundcontrol.com/master/zh/custom_build/FlyView.html)

## MAVLink 定制

QGC使用MAVLink与飞行堆栈进行通信，[MAVLink](https://mavlink.io/en/)是一种非常轻量级的消息传递协议，专为无人机生态系统而设计。QGC默认包含[ArduPilotMega](https://mavlink.io/en/messages/ardupilotmega.html).xml方言，允许它与PX4和Ardupilot进行通信（PX4使用[common.xml](https://mavlink.io/en/messages/common.html)，这在ArduPilotMega中包括）。

为了添加对一组新消息的支持，您最终需要将它们添加到或，或分叉*QGroundControl*并包含您自己的方言。`ArduPilotMega.xml common.xml`

为此：

- 替换 /[qgroundcontrol/libs/mavlink/include/mavlink](https://github.com/mavlink/qgroundcontrol/tree/master/libs/mavlink/include/mavlink) 中的预构建 C 库。
  - 默认情况下，这是一个子模块导入 https://github.com/mavlink/c_library_v2
  - 您可以更改子模块，或使用 MAVLink 工具链[构建自己的库](https://mavlink.io/en/getting_started/generate_libraries.html)。
- 您可以通过在运行 *qmake* 时将其设置为[`MAVLINK_CONF`](https://github.com/mavlink/qgroundcontrol/blob/master/QGCExternalLibs.pri#L52)来更改使用的整个方言。
