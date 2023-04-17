---
title: windows10 git等状态图标不显示
tags: 
categories: 
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-17 02:55:01/windows10 git等小图标不显示.html
mathJax: false
date: 2023-04-17 10:55:01
thumbnail:
---
IconOverlay，图标覆盖，常用于在文件（夹）上显示状态图标。

<!-- more -->

##### 原因：


Windows 支持最多15个 Overlay Icon，而它们按字符顺序排列，排在前15个的项是有效的，之后的就失效了。

##### 解决方案：

需要修改注册表，打开regedit.exe

**增加图标缓存**

位置：`计算机\HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer`

新建一个字符串值名称为 `Max Cached Icons` 值是 `4096 `，可以自定义，适当调大可以改善图标加载速度

![image-20230417112506303](..\img\image-20230417112506303.png)

**将需要显示的图标放在最前面**

位置 ：`计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers`

修改图标名称，增加空格就会排在前面

![image-20230417112046010](..\img\image-20230417112046010.png)

</br>

改完注册表后，记得重启。
