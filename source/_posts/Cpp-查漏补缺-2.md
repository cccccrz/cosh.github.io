---
title: C++查漏补缺(2)
tags:
  - c++
categories:
  - c++
  - 查漏补缺
keywords: c++ 查漏补缺
date: 2023-04-17 16:48:15
toc: true
---

> 记录一些知识点
>
> <!-- more -->

##### **mutable**

`const`修饰的成员函数，保护了成员不被修改；使用`mutable`可以突破`const`限制，常用于`lambda`函数

```cpp
class A { 
    mutable int a_;
    int b_;
public:
    void setData(int a) const 
    {
        a_ = a; // pass
        // b_ = a; // error
    }
};
```

`const`修饰的成员函数相当于修饰了`this`指针，所以也可以使用类型转换修改

```cpp
A* const pA = const_cast<A* const>(this);
pA->b_ = a; // 你得知道自己在干什么！
```

</br>

##### **`std::atomic`**

[C++原子性和内存模型 (rxsi.github.io)](https://rxsi.github.io/2022/08/16/cpp_atomic_volatile/)

[C++内存泄漏及解决方法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/458457445)

[C++ 中的原子性操作_c++中的原子性_Rain-晴天的博客-CSDN博客](https://blog.csdn.net/rain_qingtian/article/details/11020827)

[C++11中的std::atomic保证的原子性是什么_c++ atomic如何保证原子性_AlbertS的博客-CSDN博客](https://blog.csdn.net/albertsh/article/details/120611073)

[linux下C++多线程并发之原子操作与无锁编程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/149464798)[对int变量赋值的操作是原子的吗？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/27026846)


