---
title: C++查漏补缺(1)
tags:
  - c++
categories:
  - c++
  - 查漏补缺
keywords: c++ 查漏补缺
date: 2023-04-04 16:00:15
toc: true
---
记录学习中未知的知识点

<!-- more -->

##### RAII

RAII 是 resource acquisition is initialization 的缩写，意为“资源获取即初始化”。它是 C++ 之父 Bjarne Stroustrup 提出的设计理念，其核心是把资源和对象的生命周期绑定，对象创建获取资源，对象销毁释放资源。在 RAII 的指导下，C++ 把底层的资源管理问题提升到了对象生命周期管理的更高层次。

##### #define

#define 宏不受定义域限制

##### C++新增的4种类型转换

目的：克服C语言强制转换缺点

`static_cast<>()` 编译器检查

`dynamic_cast<>()`  运行期检查，根据虚表，没有static_cast高效

`const_case<>()` 去除 const , volatile 和 __unaligned, 特性

`reinterpret_cast`  重新诠释表达式类型，用于位的重新解释

##### 浮点数比较

允许误差

```cpp
// float x;
const float EPSINON = 0.00001; //  误差
if ((x <= -EPSINON) && (x >= EPSINON))
```

##### RTTI

运行时类型识别

- typeid
- dynamic_cast

##### vector释放空间

`std::vector<>()::swap`  构建空间交换，来释放空间

vector(v)
首先，通过 拷贝构造函数 创建了一个 匿名对象 ，这个匿名对象拥有v的全部数据，但是，没有空闲的空间

```cpp
std::vector<int> v;
v.reserve(100);
v.resize(5); // capacity:100
std::vector<int>(v).swap(v);  //构造匿名对象，交换空间， capacity:5
// 执行完，匿名对象析构，原空间释放
```

##### priority_queue优先队列

定义：`priority_queue<Type, Container, Functional>`

具有队列的所有特性，包括基本操作，只是在这基础上添加了内部的一个排序，它本质是一个堆实现的

##### 智能指针(smarter pointer)自定义删除器(deleter) 

`shared_ptr` 的传递删除器(deleter)方式比较简单, 只需要在参数中添加具体的删除器函数名, 即可; 注意是单参数函数;

`unique_ptr` 的删除器是函数模板(function template), 所以需要在模板类型传递删除器的类型(即函数指针(function pointer)), 再在参数中添加具体删除器;

