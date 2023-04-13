---
title: qt设置程序信息
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 06:37:47/qt设置程序信息.html
mathJax: false
date: 2023-04-13 14:37:47
thumbnail:
---
记录qt设置程序信息方法
<!-- more -->

##### Qt设置程序版本等信息以及乱码问题

在.pro文件中加入

```sh
VERSION = 1.2.3

QMAKE_TARGET_PRODUCT = 产品名称
QMAKE_TARGET_COMPANY = 公司
QMAKE_TARGET_DESCRIPTION = 文件描述
QMAKE_TARGET_COPYRIGHT = 版权
```

使用MSVC编译器会出现中文乱码，必须使用`GBK`进行编码。
下一步，通过notepad++打开文件生成目录中的`.rc`文件，通常文件名为：`项目名_resource.rc`

本文件是在项目qmake时自动生成的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ddce7a89862a4d9791876a2e1ade6e7d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcXFfNDE3NTA4MDY=,size_20,color_FFFFFF,t_70,g_se,x_16)
切换ANSI编码后乱码，由此验证了我们的编码是utf-8，而显示需要GBK：
![在这里插入图片描述](https://img-blog.csdnimg.cn/747c373ba5fe4d08880db804396ec3b9.png)
保持ANSI编码，将乱码文字替换：
![在这里插入图片描述](https://img-blog.csdnimg.cn/cbe482b8d0a44d99b86663d75e83147d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcXFfNDE3NTA4MDY=,size_20,color_FFFFFF,t_70,g_se,x_16)
在.pro文件加入，并可将上面设置注释：

```sh
#VERSION = 1.2.3
#QMAKE_TARGET_PRODUCT = 产品名称
#QMAKE_TARGET_COMPANY = 公司
#QMAKE_TARGET_DESCRIPTION = 文件描述
#QMAKE_TARGET_COPYRIGHT = 版权

RC_FILE=TEST_resource.rc
```

qmake->build后，查看.exe：

![在这里插入图片描述](https://img-blog.csdnimg.cn/086cb8a157ea4d96a08458af1a644b93.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcXFfNDE3NTA4MDY=,size_11,color_FFFFFF,t_70,g_se,x_16)

##### 总结

1、通过设置系统变量 VERSION 或 RC_ICONS （至少一个），qmake 会自动生成 .rc 文件。
以 VERSION 为例，在 pro 中添加版本信息：

```cpp
VERSION = 1.2.3 
```

执行 qmake，这时，会在 build 目录下生成一个名为 MyApp_resource.rc 的文件，生成的 .rc 文件会被自动编译和链接.
2、用Notepad++打开

切换ANS1编码，修改中文

![在这里插入图片描述](https://img-blog.csdnimg.cn/a5f8d5d9ab1c4afe899f9eeb6f544429.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcXFfNDE3NTA4MDY=,size_20,color_FFFFFF,t_70,g_se,x_16)

3、在.pro文件中删除VERSION，并添加

```cpp
#VERSION = 1.2.3 //注释或删除
RC_FILE=TEST_resource.rc
```

4、重新执行qmake,build，可完成

注意：
如果设置了 RC_FILE 或 RES_FILE，qmake 对 .rc 文件的生成就失效了。这种情况下，qmake 不会对指定的 .rc 文件或 .res 或 .o 文件作进一步更改，有关 .rc 文件生成的变量不会有任何效果。



参考文章:  
[Qt设置程序版本等信息以及乱码问题_qt 设置版本_会飞的花斑猪的博客-CSDN博客](https://blog.csdn.net/qq_41750806/article/details/122130082)
