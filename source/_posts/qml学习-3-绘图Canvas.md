---
title: qml学习-3-绘图Canvas
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-18 02:04:45/qml学习-x-绘图Canvas.html
mathJax: false
date: 2023-07-18 10:04:45
thumbnail:
---
> Qt5中引进了画布元素（canvas element），允许脚本绘制。画布元素 （canvas element）提供了⼀个依赖于分辨率的位图画布，你可以使用 JavaScript脚本来绘制图形，制作游戏或者其它的动态图像。画布元素 （canvas element）是基于HTML5的画布元素来完成的。
<!-- more -->

#### **画布元素（canvas element）**

**画布元素（canvas element）**的基本思想是使用⼀个2D对象来渲染路径。这个2D对象包括了必要的绘图函数，画布元素（canvas element）充当绘制画布。2D对象⽀持画笔，填充，渐变，⽂本和绘制路径创建命令。

画布自身提供了典型的⼆维笛卡尔坐标系统，左上角是`(0,0)`坐标。`Y`轴坐标轴向下，`X`轴坐标轴向右。 

**典型绘制命令调用如下**： 

1. 装载画笔或者填充模式
2. 创建绘制路径
3. 使用画笔或者填充绘制路径

```qml
import QtQuick 2.0 

Canvas { 
    id: root 
    width: 200; height: 200 

    // 为绘图而重写的处理函数 
    onPaint: { 
        // 获取2D对象
        var ctx = getContext("2d") 
        // 描边风格
        ctx.lineWidth = 4 
        ctx.strokeStyle = "blue" 
        // 填充风格
        ctx.fillStyle = "steelblue" 
        
        // 创建新的绘制路径
        ctx.beginPath() 
        // 设置绘制起点 
        ctx.moveTo(50,50) 
        // 绘制路径移动 
        ctx.lineTo(150,50) 
        ctx.lineTo(150,150) 
        ctx.lineTo(50,150) 
        // 闭合绘制路径 ---- 一个起点(50,50) 100x100的矩形
        ctx.closePath() 
        
        // 使用填充风格填充绘制路径
        ctx.fill() 
        // 使用描边风格描边
        ctx.stroke() 
    } 
} 
```

**注意**

通常在你重置了路径后你将会设置⼀个开始点，所以，在`beginPath()`这个操作后，你需要使用`moveTo`来设置开始点。

在绘制矩形时，Qt提供了⼀个**便捷的接口**，而不需要调用`stroke`或者`fill`来完成。

```js
// 画一个填充矩形
ctx.fillRect(20, 20, 80, 80) 
// 切除一个矩形 
ctx.clearRect(30, 30, 60, 60) 
// 画一个描边矩形 
ctx.strokeRect(20, 20, 40, 40) 
```

**注意**

画笔的绘制 `(stroke)` 区域**由中间向两边延展**。⼀个宽度为4像素的画笔将会在绘制路径的⾥⾯绘制2个像素，外⾯绘制2个像素。

</br>

#### **渐变（Gradients）**

画布中可以使用颜⾊填充也可以使用渐变或者图像来填充。 

```qml
onPaint: { 
    var ctx = getContext("2d") 
    var gradient = ctx.createLinearGradient(100,0,100,200) 
    gradient.addColorStop(0, "blue") 
    gradient.addColorStop(0.5, "lightsteelblue") 
    ctx.fillStyle = gradient 
    ctx.fillRect(50,50,100,100) 
} 
```

在这个例⼦中，渐变色定义在开始点（100,0）到结束点（100,200）。在我们画布中是⼀个中间垂直的线。渐变色在停止点定义⼀个颜⾊，范围从0.0到 1.0。这⾥我们使用⼀个蓝色作为0.0（100,0），⼀个高亮蓝色作为 0.5（100,200）。渐变色的定义比我们想要绘制的矩形更⼤，所以矩形在它定义的范围内对渐变进⾏了裁剪。

**注意**

渐变⾊是在画布坐标下定义的，而不是在绘制路径相对坐标下定义的。画布中没有相对坐标的概念。

</br>

#### **阴影（Shadows）**

2D对象的路径可以使用阴影增强显示效果。阴影是⼀个区域的轮廓线使用偏移量，颜色和模糊来实现的。所以你需要指定⼀个阴影颜⾊ （`shadowColor`），阴影X轴偏移值（`shadowOffsetX`），阴影Y轴偏移值 （`shadowOffsetY`）和阴影模糊（`shadowBlur`）。这些参数的定义都使用2D context来定义。2D context是唯⼀的绘制操作接口。

```js
// 绘制⿊⾊背景 
ctx.strokeStyle = "#333" 
ctx.fillRect(0,0,canvas.width,canvas.height);
// 阴影配置
ctx.shadowColor = "blue"; 
ctx.shadowOffsetX = 2; 
ctx.shadowOffsetY = 2; 
ctx.shadowBlur = 10;

// 绘制
ctx.font = 'Bold 80px Ubuntu';
ctx.fillStyle = "#33a9ff"; 
ctx.fillText("Earth",30,180);
```

</br>

#### **图片（Images）**

QML画布⽀持多种资源的图片绘制。在画布中使用⼀个图片需要先加载图片资源。下面例子我们使用`Component.onCompleted`操作来加载图片。 

```qml
onPaint: {
    var ctx = getContext("2d")

    // draw an image
    ctx.drawImage('assets/ball.png', 10, 10)

    // 记录绘图设置
    ctx.save()
    // 移动画布原点, 相当于平移
    ctx.translate(100,0)
    ctx.strokeStyle = 'red'
    // 创建绘制路径 -- 三角形
    ctx.beginPath()
    ctx.moveTo(10,10)
    ctx.lineTo(55,10)
    ctx.lineTo(35,55)
    ctx.closePath()
    // 从当前路径创建裁剪区域
    ctx.clip()
    // 绘制图像 ---- 被裁剪
    ctx.drawImage('assets/ball.png', 10, 10)
    // 描边
    ctx.stroke()
    // 恢复绘图设置
    ctx.restore()
}

Component.onCompleted: {
	loadImage("assets/ball.png")
}
```

裁剪操作步骤：

1. 创建绘图路径`beginPath()`  `closePath()`
2. 调用`clip()`创建裁剪区域

</br>

#### **转换（Transformation）**

画布有多种方式来转换坐标系。这些操作非常类似于QML元素的转换。你可以通过缩放（`scale`），旋转（`rotate`），平移（`translate`）来转换坐标系。 与QML元素的转换不同的是，转换原点通常就是画布原点。例如，从中心点放大⼀个封闭的路径，你需要先将画布原点移动到整个封闭的路径的中心点上。使用这些转换的方法你可以创建⼀些更加复杂的转换。

```qml
Canvas {
    id: root
    width: 240; height: 120
    onPaint: {
        var ctx = getContext("2d")
        ctx.lineWidth = 4;
        ctx.strokeStyle = "blue";

        // 将画布中心置为原点
        ctx.translate(root.width/2, root.height/2);

        // draw path
        ctx.beginPath();
        ctx.rect(-20, -20, 40, 40);
        ctx.stroke();

        // 旋转坐标系
        ctx.rotate(Math.PI/4);
        ctx.strokeStyle = "green";

        // draw path
        ctx.beginPath();
        ctx.rect(-20, -20, 40, 40);
        ctx.stroke();
    }
} 
```

除了移动画布外，也可以使⽤`scale(x,y)`来缩放x,y坐标轴。旋转使用 `rotate(angle)`，angle是角度（360度=2*Math.PI）。使用 `setTransform(m11,m12,m21,m22,dx,dy)`来完成矩阵转换。

**警告**

QML画布中的转换与HTML5画布中的机制有些不同。不确定这是不是⼀个 Bug。 

**注意**

重置矩阵你可以调⽤`resetTransform()`函数来完成，这个函数会将转换矩阵还原为单位矩阵。

