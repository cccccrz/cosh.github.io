---
title: QList去重
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 07:02:37/QList去重.html
mathJax: false
date: 2023-04-13 15:02:37
thumbnail:
---
`QSet<T> QList::toSet() const`
<!-- more -->

有需要，还可以转回来

`QList<T> QSet::toList() const`

如果`typename T`为自定义对象

```cpp
struct T
{
    qint64 size;
    QString data;
    QString uuid;
};
```

报错：

```cpp
QList<T> list;
list.toSet(); // error: C2665: "qHash" balabala
```

因为自定义对象需要提供**全局**的`operator==()`和一个**全局**的`qHash()`

补充定义：

```cpp
inline bool operator==(const T& left, const T& right) { return left.uuid == right.uuid; }
Q_DECL_PURE_FUNCTION inline int qHash(const T& key, uint seed = 0) Q_DECL_NOTHROW { return qHash(key.uuid, seed); }
```

验证一下：

```cpp
list.toSet(); // ok
```

