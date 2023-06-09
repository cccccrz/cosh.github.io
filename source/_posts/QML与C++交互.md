---
title: QML与C++交互
tags:
  - qt
  - QML
categories:
  - qt
  - QML
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-06-1 11:26:24/QML与C++交互.html
mathJax: false
date: 2023-06-1 11:26:24
thumbnail:
---

> Data Type Conversion Between QML and C++
>
> <!-- more -->

## 1.变量交互

### 方法一

> `QQmlContext::setContextProperty`直接将cpp中的变量注册进qml

#### 1.声明

C++类中使用 `Q_PROPERTY` 宏声明一个可以从QML访问的属性 `m_age`

```cpp
Q_PROPERTY(QString m_age READ getAge WRITE setAge NOTIFY ageChanged)
```

#### 2.注册

定义一个C++类，使用`QQmlContex`的`setContextProperty()`成员函数向QML运行上下文环境注册C++类型

```cpp
#include <QQmlContext>
Datas datas; // 自定义对象Datas
QQmlContext *qmlctx = engine.rootContext();
qmlctx->setContextProperty("cpp_datas",&datas); //注册datas，qml变量名为cpp_datas
```

`Q_INVOKABLE` 声明函数

#### 3.使用

```qml
// qml文件
cpp_datas.m_age
```



### 方法二

`qmlRegisterSingletonType` 模板函数注册一个单例类供Qml使用

`qmlRegisterUncreatableType` 注册一个QML不可实例化的类型

搭配使用，可以在cpp中管理对象，在QML中使用单例对象操作交互

#### 1.声明

自定义全局单例类`MyQmlGlocalControl`，管理QML使用的对象

```cpp
class MyQmlGlocalControl : public QObject
{
    Q_OBJECT
public:
    explicit MyQmlGlocalControl(QObject *parent = nullptr) {
		// cpp中实例化对象
        _testManager = new TestManager;
    }

    Q_PROPERTY(TestManager* testManager READ testManager CONSTANT)

    TestManager* testManager() { return _testManager; }

private:
    TestManager* _testManager;
};
```

需要与QML 交互的对象 `TestManager`

```cpp
class TestManager : public QObject
{
    Q_OBJECT
public:
    explicit TestManager(QObject *parent = nullptr);
    
    Q_PROPERTY(int age READ age NOTIFY ageChanged)
        
    int age() { return _age; }

signals:
    void ageChanged();

private:
    int _age = 5;
};
```

#### 2.注册

```cpp
static QObject* myQmlGlobalSingletonFactory(QQmlEngine*, QJSEngine*)
{
    MyQmlGlocalControl* qmlGlobal = new MyQmlGlocalControl;
    return qmlGlobal;
}
```

```cpp
// 模块名MyQml, QML中使用全局对象名MyQml
qmlRegisterUncreatableType<TestManager>("MyQml", 1, 0, "TestManager", "Reference only");
qmlRegisterSingletonType<MyQmlGlocalControl>("MyQml", 1, 0, "MyQml", myQmlGlobalSingletonFactory); 
```

#### 3.使用

```qml
import MyQml 1.0
{
	property var _testManager: MyQml.testManager // MyQmlGlocalControl::testManager()
    property int _age: MyQml.testManager.age
}
```



## 2.信号交互

在变量交互的基础上，使用`Connections`建立cpp/QML信号与QML槽的连接

```qml
Connections {
    target: _testManager
    // Qt Version > 5.15
    function onAgeChanged() {
    	console.log("age changed :", _age)
    }
    // Qt Version <
    onAgeChanged: {
    	...
    }
}
```

