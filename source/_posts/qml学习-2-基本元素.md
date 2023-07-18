---
title: qml学习-2-基本元素
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-14 09:29:56/qml学习-2-基本元素.html
mathJax: false
date: 2023-07-14 17:29:56
---
> Basic Elements
<!-- more -->

元素可以被分为可视化元素与非可视化元素。⼀个可视化元素（例如矩形框 Rectangle）有着几何形状并且可以在屏幕上显示。⼀个非可视化元素（例如 计时器Timer）提供了常用的功能，通常用于操作可视化元素。 现在我们将专注于几个基础的可视化元素，例如Item（基础元素对象）， Rectangle（矩形框），Text（文本），Image（图像）和MouseArea（鼠标区域）。

</br>

####  基础元素对象（Item Element）

`Item`（基础元素对象）是所有可视化元素的基础对象，所有其它的可视化元素都继承自`Item`。它自身不会有任何绘制操作，但是定义了所有可视化元素共有的属性：

| Group（分组）                | Properties（属性）                                           |
| ---------------------------- | ------------------------------------------------------------ |
| Geometry（几何属性）         | x,y（坐标）定义了元素左上⾓的位置，width，height（⻓ 和宽）定义元素的显示范围，z（堆叠次序）定义元素之间 的重叠顺序。 |
| Layout handing（布局操作）   | anchors（锚点），包括左（left），右（right），上 （top），下（bottom），水平与垂直居中（vertical center，horizontal center），与margins（间距）⼀起定义了元素与其它元素之间的位置关系。 |
| Key handing（按键操作）      | 附加属性key（按键）和keyNavigation（按键定位）属性来控制按键操作，处理输⼊焦点（focus）可用操作。 |
| Transformation（转换）       | 缩放（scale）和rotate（旋转）转换，通用的x,y,z属性列表转换（transform），旋转基点设置 （transformOrigin）。 |
| Visual（可视化）             | 不透明度（opacity）控制透明度，visible（是否可⻅）控制元素是否显示，clip（裁剪）用来限制元素边界的绘制， smooth（平滑）用来提⾼渲染质量。 |
| State definition（状态定义） | states（状态列表属性）提供了元素当前所⽀持的状态列 表，当前属性的改变也可以使用transitions（转变）属性列 表来定义状态转变动画。 |

`Item`（基本元素对象）通常被用来作为其它元素的容器使用，类似HTML语 ⾔中的div元素（div element）。

</br>

#### Rectangle（矩形元素）

Rectangle（矩形框）是基本元素对象的⼀个扩展，增加了⼀个颜色来填充它。它还⽀持边界的定义，使用`border.color`（边界颜色）， `border.width`（边界宽度）来自定义边界。你可以使用`radius`（半径）属性来 创建⼀个圆⾓矩形。

```qml
Rectangle { 
    id: rect1 
    x: 112; y: 12 
    width: 76; height: 96 
    border.color: "lightsteelblue" 
    border.width: 4 
    radius: 8 
}
```

注意颜色的命名是来自SVG颜色的名称（查看[CSS Color Module Level 3)](https://www.w3.org/TR/css-color-3/#svg-color)可以获取更多的颜色名称）。你也可以使用其它的方法来 指定颜色，比如RGB字符串（'#FF4444'），或者⼀个颜色名字（例 如'white'）。 此外，填充的颜色与矩形的边框也⽀持自定义的渐变色。

```qml
Rectangle {
    id: rect1 
    x: 12; y: 12 
    width: 176; height: 96 
    gradient: Gradient { 
        GradientStop { position: 0.0; color: "lightsteelblue" } 
        GradientStop { position: 1.0; color: "slategray" } 
    }
    border.color: "slategray" 
}
```

⼀个渐变色是由⼀系列的梯度值定义的。每⼀个值定义了⼀个位置与颜色。 位置标记了y轴上的位置（0 = 顶，1 = 底）。GradientStop（倾斜点）的颜色标记了颜色的位置。 

**注意**

⼀个矩形框如果没有width/height（宽度与⾼度）**将不可⻅**。如果你有几个相互关联width/height（宽度与⾼度）的矩形框，在你组合逻辑中出了错后可能就会发⽣矩形框不可⻅，请注意这⼀点。 

**注意**

这个函数**⽆法创建⼀个梯形**，最好使用⼀个已有的图像来创建梯形。有⼀种可能是在旋转梯形时，旋转的矩形几何结构不会发⽣改变，但是这会导致几何元素相同的可⻅区域的混淆。从作者的观点来看类似的情况下最好使用设计好的梯形图形来完成绘制。

</br>

#### Text（文本元素）

显示文本你需要使用Text元素（Text Element）。它最值得注意的属性时字符串类型的text属性。这个元素会使用给出的text（文本）与font（字体）来计算初始化的宽度与⾼度。可以使用字体属性组来（font property group）来改变当前的字体，例如font.family，font.pixelSize，等等。改变文本的颜色值只需要改变颜色属性就可以了。

```qml
Text {
    text: "The quick brown fox" 
    color: "#303030" 
    font.family: "Ubuntu" 
    font.pixelSize: 28 
}
```

文本可以使用`horizontalAlignment`与`verticalAlignment`属性来设置它的对⻬效果。为了提⾼文本的渲染效果，你可以使用`style`和`styleColor`属性来配置文字的外框效果，浮雕效果或者凹陷效果。对于过⻓的文本，你可能需要使用省略号来表示，例如`A very ... long text`，你可以使用`elide`属性来完成这个操 作。`elide`属性允许你设置文本左边，右边或者中间的省略位置。如果你不 想'....'省略号出现，并且希望使用文字换⾏的方式显示所有的文本，你可以使用`wrapMode`属性（这个属性只在明确设置了宽度后才⽣效）：

```qml
Text {
    width: 40; height: 120 
    text: 'A very long text' 

    // '...' shall appear in the middle 
    elide: Text.ElideMiddle 

    // red sunken text styling 
    style: Text.Sunken 
    styleColor: '#FF4444' 

    // align text to the top 
    verticalAlignment: Text.AlignTop 

    // only sensible when no elide mode 
    // wrapMode: Text.WordWrap 
}
```

⼀个text元素（text element）只显示的文本，它不会渲染任何背景修饰。除了显示的文本，text元素背景是透明的。为⼀个文本元素提供背景是你自⼰需要考虑的问题。 

**注意**

知道⼀个文本元素（Text Element）的初始宽度与⾼度是依赖于文本字符串和设置的字体这⼀点很重要。⼀个**没有设置宽度或者文本的文本元素（Text Element）将不可⻅**，默认的初始宽度是0。

**注意**

通常你想要对文本元素布局时，你需要区分文本在文本元素内部的边界对⻬和由元素边界自动对⻬。前⼀种情况你需要使用`horizontalAlignment`和 `verticalAlignment`属性来完成，后⼀种情况你需要操作元素的几何形状或者使用`anchors`（锚定）来完成。

</br>

#### Image（图像元素）

⼀个图像元素（Image Element）能够显示不同格式的图像（例如 PNG,JPG,GIF,BMP）。想要知道更加详细的图像格式⽀持信息，可以查看 Qt的相关文档。`source`属性（source property）提供了图像文件的链接信息，`fillMode`（文件模式）属性能够控制元素对象的⼤⼩调整⾏为。

```qml
Image { 
    x: 12; y: 12 
    // width: 48 
    // height: 118 
    source: "assets/rocket.png" 
}
Image { 
    x: 112; y: 12 
    width: 48 
    // 减少了高
    height: 118/2 
    source: "assets/rocket.png" 
    fillMode: Image.PreserveAspectCrop 
    // 允许裁剪
    clip: true 
} // 此图只显示部分
```

**注意**

⼀个URL可以是使用'/'语法的本地路径（"./images/home.png"）或者⼀个网络链接（"http://example.org/home.png"）。

**注意**

图像元素（Image element）使用`PreserveAspectCrop`可以避免裁剪图像 数据被渲染到图像边界外。**默认情况下裁剪是被禁用的**（`clip:false`）。你需 要打开裁剪（`clip:true`）来约束边界矩形的绘制。这对任何可视化元素都是有效的。 建议使用QQmlImageProvider你可以通过C++代码来创建自⼰的图像提供器，这允许你动态创建图像并且使用线程加载。

</br>

#### MouseArea（鼠标区域元素）

为了与不同的元素交互，你通常需要使用`MouseArea`（鼠标区域）元素。这是⼀个矩形的非可视化元素对象，你可以通过它来捕捉鼠标事件。当用户与可视化端口交互时，`mouseArea`通常被用来与可视化元素对象⼀起执⾏命令。

```qml
Rectangle { 
    id: rect1
    x: 12; y: 12 
    width: 76; height: 96 
    color: "lightsteelblue" 
    MouseArea { 
        id: area 
        width: parent.width 
        height: parent.height 
        onClicked: rect2.visible = !rect2.visible 
    } 
}
Rectangle { 
    id: rect2 
    x: 112; y: 12 
    width: 76; height: 96 
    border.color: "lightsteelblue" 
    border.width: 4 
    radius: 8 
}
```

**注意**

这是QtQuick中非常重要的概念，输⼊处理与可视化显示分开。这样你的交互区域可以比你显示的区域⼤很多。
