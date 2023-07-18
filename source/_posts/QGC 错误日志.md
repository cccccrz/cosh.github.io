---
title: QGC 错误日志
tags:
  - qt
  - QGC
categories:
  - qt
  - QGC
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-06-9 13:56:24/QGC 错误日志.html
mathJax: false
date: 2023-06-9 13:56:24
thumbnail:
---

> 记录QGC二次开发遇到的一些问题
>
> <!-- more -->

### Q&A

1.Linux 安装GStreamer后编译报错：g_once_init_enter ` ‘__atomic_load’ must not be a pointer to a ‘volatile’ type`

解决方案：

删除错误处的 volatile 声明

相关链接：[GCC 11 的构建错误（原子作为易失性和循环构造） ·问题 #9669 ·马夫林克/Q地面控制 ·GitHub](https://github.com/mavlink/qgroundcontrol/issues/9669)

新版本官方已修复
