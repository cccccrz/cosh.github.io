---
title: qml学习-1
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-05-10 03:26:24/qml学习-1.html
mathJax: false
date: 2023-05-10 11:26:24
thumbnail:
---
初识js，qml
<!-- more -->

QML是⼀种描述⽤户界⾯的声明式语⾔。它将⽤户界⾯分解成⼀些更⼩的元素，这些元素能够结合成⼀个组件。QML语⾔描述了⽤户界⾯元素的形状和⾏为。⽤户界⾯能够使⽤JavaScript来提供修饰，或者增加更加复杂的逻辑。从这个⾓度来看它遵循HTML-JavaScript模式，但QML是被设计⽤来描述⽤户界⾯的，⽽不是⽂本⽂档。 

从QML元素的层次结构来理解是最简单的学习⽅式。⼦元素从⽗元素上继承了坐标系统，它的x,y坐标总是相对应于它的⽗元素坐标系统。 

#### 语法（QML Syntax）

一个小例子

```qml
// rectangle.qml 
import QtQuick 2.0 

// 根元素
Rectangle { 
    // 命名为 root
    id: root 
    
    // 属性 <name>: <value> 
    width: 120; height: 240 

    // color property 
    color: "#D8D8D8" 

    // 子元素 
    Image { 
        id: rocket 

        // 引用父元素 
        x: (parent.width - width)/2; y: 40 
        source: 'assets/rocket.png' 
    }

    // Another child of root 
    Text {
        // 无命名 
        
        // 用id引用元素
        y: rocket.y + rocket.height + 20 

        // 引用root元素
        width: root.width 				
        horizontalAlignment: Text.AlignHCenter 
        text: 'Rocket' 
    } 
}
```

- import声明导⼊了⼀个指定的模块版本。⼀般来说会导⼊QtQuick2.0来作为初始元素的引⽤。
- 使⽤//可以单⾏注释，使⽤/**/可以多⾏注释，就像C/C++和JavaScript⼀样。 
- 每⼀个QML⽂件都需要⼀个根元素，就像HTML⼀样。 
- ⼀个元素使⽤它的类型声明，然后使⽤{}进⾏包含。 
- 元素拥有属性，他们按照`name:value`的格式来赋值。 
- 任何在QML⽂档中的元素都可以使⽤它们的id进⾏访问（id是⼀个任意的标识符）。 
- 元素可以嵌套，这意味着⼀个⽗元素可以拥有多个⼦元素。⼦元素可以通过访问parent关键字来访问它们的⽗元素。 

**建议**

你会经常使⽤id或者关键字`parent`来访问你的⽗对象。有⼀个⽐较好的⽅法 是命名你的根元素对象id为root（`id:root`），这样就不⽤去思考你的QML⽂档中的根元素应该⽤什么⽅式命名了。

**提示**

可以在命令行使用QtQuick运行环境来运行qml

```sh
$QTDIR/bin/qmlscene rectangle.qml
```



#### 属性（property）

元素使⽤他们的元素类型名进⾏声明，使⽤它们的属性或者创建⾃定义属性来定义。⼀个属性对应⼀个值，例如 `width:100，text: 'Greeting', color: '#FF0000'`。⼀个属性有⼀个类型定义并且需要⼀个初始值。

```qml
Text {
    // (1) identifier 
    id: thisLabel 

    // (2) set x- and y-position 
    x: 24; y: 16 

    // (3) 属性绑定: bind height to 2 * width 
    height: 2 * width 

    // (4) 自定义属性
    property int times: 24 

    // (5) 属性别名 
    property alias anotherTimes: thisLabel.times 

    // (6) 属性绑定: set text appended by value 
    text: "Greetings " + times 

    // (7) font是一个分组属性 
    font.family: "Ubuntu" 
    font.pixelSize: 24 

    // (8) KeyNavigation是一个附加属性
    KeyNavigation.tab: otherLabel

    // (9) 属性改变触发的槽 
    onHeightChanged: console.log('height:', height) 

    // 接收键盘事件需要焦点
    focus: true 

    // 属性绑定:根据焦点值改变颜色 
    color: focus?"red":"black" 
}
```

1. id是⼀个⾮常特殊的属性值，它在⼀个QML⽂件中被⽤来引⽤元素。id 不是⼀个字符串，⽽是⼀个标识符和QML语法的⼀部分。⼀个id在⼀个 QML⽂档中是唯⼀的，并且不能被设置为其它值，也⽆法被查询（它的⾏为更像C++世界⾥的指针）。 

2. ⼀个属性能够设置⼀个值，这个值依赖于它的类型。如果没有对⼀个属性赋值，那么它将会被初始化为⼀个默认值。你可以查看特定的元素的⽂档来获得这些初始值的信息。

3. ⼀个属性能够依赖⼀个或多个其它的属性，这种操作称作**属性绑定**。**当它依赖的属性改变时，它的值也会更新。这就像订了⼀个协议**，在这个例⼦中height始终是width的两倍。 

4.  添加⾃⼰定义的属性需要使⽤`property`修饰符，然后跟上类型，名字和可 选择的初始化值（`property :` ）。如果没有初始值将会给定⼀个系统初始 值作为初始值。注意如果属性名与已定义的默认属性名不重复，使⽤default关键字你可以将⼀个属性定义为默认属性。这在你添加⼦元素时⽤得着，如果他们是可视化的元素，⼦元素会⾃动的添加默认属性的⼦类型链表（children property list）。 

5. 另⼀个重要的声明属性的⽅法是使⽤`alias`关键字（`property alias : `）。 `alias`关键字允许我们转发⼀个属性或者转发⼀个属性对象⾃⾝到另⼀个作⽤域。我们将在后⾯定义组件导出内部属性或者引⽤根级元素id会使⽤到这个技术。⼀个属性别名不需要类型，它使⽤引⽤的属性类型或者对象类型。

6.  text属性依赖于⾃定义的timers（int整型数据类型）属性。**int整型数据会⾃动的转换为string字符串类型数据**。这样的表达⽅式本⾝也是另⼀种**属性绑定**的例⼦，⽂本结果会在times属性每次改变时刷新。 

7. ⼀些属性是按组分配的属性。当⼀个属性需要结构化并且相关的属性需要联系在⼀起时，我们可以这样使⽤它。另⼀个组属性的编码⽅式是 

   ```qml
   font { family: "UBuntu"; pixelSize: 24 }
   ```

8. ⼀些属性是元素⾃⾝的附加属性。这样做是为了全局的相关元素在应⽤程序中只出现⼀次（例如键盘输⼊）。

9.  对于每个元素你都可以提供⼀个信号操作。这个操作在属性值改变时被调⽤。例如这⾥我们完成了当height（⾼度）改变时会使⽤控制台输出 ⼀个信息

**警告**

**⼀个元素id应该只在当前⽂档中被引⽤**。QML提供了动态作⽤域的机制，**后加载的⽂档会覆盖之前加载⽂档的元素id号，这样就可以引⽤已加载并且没有被覆盖的元素id**，这有点类似创建全局变量。但不幸的是这样的代码阅读性很差。目前还没有办法解决这个问题，所以你使⽤这个机制的时候最好仔细⼀些甚⾄不要使⽤这种机制。如果你想向⽂档外提供元素的调⽤，你可以在根元素上使⽤属性导出的⽅式来提供。

#### 脚本（Scripting）

```qml
Text {
    id: label x: 24; y: 24 

    // custom counter property for space presses
    property int spacePresses: 0 
    text: "Space pressed: " + spacePresses + " times"

    // (1) handler for text changes 
    onTextChanged: console.log("text changed to:", text) 

    // need focus to receive key events 
    focus: true 

    // (2) handler with some JS
    Keys.onSpacePressed: { increment() }

    // clear the text on escape 
    Keys.onEscapePressed: { label.text = '' }

    // (3) a JS function 
    function increment() { spacePresses = spacePresses + 1 } 
}
```

1. ⽂本改变操作onTextChanged会将每次空格键按下导致的⽂本改变输出 到控制台。 
2. 当⽂本元素接收到空格键操作（⽤户在键盘上点击空格键），会调⽤ JavaScript函数increment()。 
3. 定义⼀个JavaScript函数使⽤这种格式function (){....}，在这个例⼦中是 增加spacePressed的计数。每次spacePressed的增加都会导致它绑定的 属性更新。 

**注意**

QML的：（属性绑定）与JavaScript的=（赋值）是不同的。绑定是⼀个协议，并且存在于整个⽣命周期。然⽽JavaScript赋值（=）只会产⽣⼀次效果。当⼀个新的绑定⽣效或者使⽤JavaScript赋值给属性时，绑定的⽣命周期就会结束。例如⼀个按键的操作设置⽂本属性为⼀个空的字符串将会销毁我们的增值显⽰：`Keys.onEscapePressed: { label.text = '' }`在点击取消（ESC）后，再次点击空格键（space-bar）将不会更新我们的显⽰，之前的text属性绑定（`text: "Space pressed:" + spacePresses + "times"`)被销毁。

当你对改变属性的策略有冲突时（⽂本的改变基于⼀个增值的绑定并且可以被JavaScript赋值清零），类似于这个例⼦，你最好不要使⽤绑定属性。你需要使⽤赋值的⽅式来改变属性，属性绑定会在赋值操作后被销毁（销毁协议！）

