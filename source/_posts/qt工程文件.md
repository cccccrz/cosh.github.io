---
title: Qt工程文件
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 06:22:31/qt工程文件.html
mathJax: false
date: 2023-04-13 14:22:31
thumbnail:
---
记录Qt工程文件实用语法

<!-- more -->

##### 打印信息

`message()` 打印信息

`!build_pass:`  防止重复打印

```sh
# 打印变量
!build_pass:message('------------- $$PWD -------------')
```

##### 增加预编译宏

```sh
win32:CONFIG(debug, debug|release): DEFINES += DEBUG
else:win32:CONFIG(release, debug|release): DEFINES += RELEASE
```

`QMAKE_POST_LINK` 链接前执行

```sh
# 链接前拷贝
LIB_FILE = xx/xx/xx
LIB_BIN_PATH = xx/xx
win32: QMAKE_POST_LINK += copy /Y $$LIB_FILE $$LIB_BIN_PATH
else:unix: QMAKE_POST_LINK += cp $$LIB_FILE $$LIB_BIN_PATH
```

##### 指定目标生成路径

```sh
BULID_PATH = $$PWD/bin
win32:CONFIG(debug, debug|release): BULID_PATH = $$BULID_PATH/debug
else:win32:CONFIG(release, debug|release): BULID_PATH = $$BULID_PATH/release
DESTDIR = $$BULID_PATH
```

##### 递归添加源文件

```sh
SourceDir = \
	$$PWD/common \
	$$PWD/main
for(var, SourceDir) {
	SOURCES += $$files($$join(var, , , /*.cpp), true)
	HEADERS += $$files($$join(var, , , /*.h), true)
	FORMS += $$files($$join(var, , , /*.ui), true)
}
# 去重
SOURCES = $$unique(SOURCES)
HEADERS = $$unique(HEADERS)
INCLUDEPATH += $$dirname(SOURCES)
INCLUDEPATH = $$unique(INCLUDEPATH)
```

##### 添加库依赖

```sh
ARCH = x86_64
PLANTFORM = linux
LibPaths = \
	$$PWD/xxx1/$$PLANTFORM/$$ARCH \
	$$PWD/xxx2/$$PLANTFORM/$$ARCH
for(var, LibPaths) {
	LibName = $$files($$join(var, , , /*.so), true)
	# 取库名
	LibName = $$basename(LibName)
	# 去掉后缀, libxxx.so, 换成 -lxxx
	LibName = $$replace(LibName, $$quote(.so), )
	LibName = $$replace(LibName, $$quote(\.a), )
	#LibName = $$replace(LibName, lib, -l)
	# 取库路径
	LibPath = $$dirname(var)
	#LibPath = $$join(LibPath, $$quote(" -L"), -L, )
	#添加库
	LIBS += -L$$LibPath -l$$LibName
}
```

##### 指定缓存路径

```
UI_DIR
RCC_DIR
MOC_DIR
OBJECTS_DIR
```

##### 指定版本

[Qt设置程序版本等信息以及乱码问题](https://blog.csdn.net/qq_41750806/article/details/122130082)

```sh
VERSION = 1.2.3

QMAKE_TARGET_PRODUCT = 产品名称
QMAKE_TARGET_COMPANY = 公司
QMAKE_TARGET_DESCRIPTION = 文件描述
QMAKE_TARGET_COPYRIGHT = 版权
```

构建生成rc文件，删除上面代码添加RC文件

```sh
RC_FILE=TEST_resource.rc
```



##### 函数

```sh
message()
功能：常用的pro打印，类似printf，Qt5中可以使用（Qt4不知道为什么不能用，可以在Qt5上调试好后放到Qt4上）
示例：
	message("My Source Files have $$SOURCES")
	将会打印出 My Source Files have ./src/main.cpp ./src/func.cpp
提示：有什么奇奇怪怪的想法可以用message来进行打印出来验证

$$files(1, 2) 
功能：递归搜索文件
参数：
	1：搜寻的文件模板路径有.cpp文件
	2：是否递归搜索，true - 是， false - 否
示例：
	Sample = ./src/*.cpp
	$$files(Sample, true)
	返回
	./src/main.cpp
	./src/func.cpp


$$join(1, 2, 3, 4) 
功能：变量拼接内容
参数：
	1：变量
	2：“胶水”，在多变量之间拼接上去的词
	3：头部拼接
	4：尾部拼接
示例1：
	Sample = LibName 
	$$join(Sample, , , .so)
	返回 LibName.so
示例2：
	Sample = Dxf
	$$join(Sample, , -l, )
	返回 -lDxf

$$section(1, 2, 3, 4)
功能：字符串节选
参数：	
	1：字符串
	2：分割号
	3：开始段
	4：结束段
示例：
	Sample = LibSample.so
	$$section(Sample, ., 0, 0)
	返回 LibSample

$$dirname
功能：返回文件的路径，不带文件名的

$$basename
功能：返回文件名，不带路径的

$$unique
功能：去掉重复的变量
```
