---
title: Qt PropertyBrowser安装与配置 
date: 2010-04-03 07:00:13
categories: web
tags: [QT]
---
简介：

Qt PropertyBrowser提供了简便地用于编辑Properties的方法。

- 下载:
http://qt.nokia.com/products/appdev/add-on-products/catalog/4/Widgets/qtpropertybrowser
提供了介绍及下载链接。

- 编译：

*NIX与Mac平台的编译比较简单，直接看INSTALL文档即可。下面介绍一下

Windows平台的编译:

将源码解压到一个特定的目录（eg. D:\DevTools\Qt\qt\include\qtpropertybrowser)， 然后用Windows PlatformSDK的终端到特定的目录，这里假定你已经装过Qt,并将其编译成VS可用的版本。

编译examples:

利用以下方法来看下propertybrowser的例子的运行效果。

首先，如果你不需要用Visual Studio的版本，你可以直接用QtCreator打开.pro文件进行编译，然后运行。这里给出适用于Visual Studio的版本。用qmake生成适用于VS的nmake的Makefile(也可以生成.sln文件，但用Makefile在此处更方便些)：

qmake –spec win32-msvc2005 –win32 –o Makefile qtpropertybrowser.pro

此时在同个目录下生成了Makefile,然后在现有的debug窗口下（主要是现有的platformsdk的窗口自动包含了visual studio的一些可执行程序的路径，你也可以手动设置这些路径，然后直接在普通的cmd窗口下运行）执行：

nmake release

编译完成后，进入examples下的各个子目录则可以查看附带例子的运行效果。

编译libs

如上编译了例子后，并没有生成对应的.lib及.dll文件供以后自己写程序的时候复用，因此，进入buildlib目录，其中有一名为buildlib.pro的文件，同样使用qmake生成适用于VS2005的Nmake的Makefile如下：

Qmake –spec win32-msvc2005 –win32 –o Makefile buildlib.pro

接着，运行nmake release (当然可继续选择nmake debug)

编译完成后，可发现在目录lib中多了一些.dll,.lib,.exp文件，这些则可供后续写程序的时候调用。

继续使用nmake install则可以自动将这些生成的.dll,文件拷贝一份到Qt的bin目录。

以上的准备工作完成了，下面用一个例子测试一下，就用其examples中的simple来做测试吧。把其代码main.cpp拷贝到另一个独立的目录(eg. D:\Test)，为其写一个CMakeLists.txt如下：
``` bash
project(test_property)

cmake_minimum_required(VERSION 2.6)

find_package(Qt4 REQUIRED)

include(${QT_USE_FILE})

#for Win32 settings, especially on my development environment

include_directories("D:\DevTools\Qt\qt\include\qtpropertybrowser\src")

link_directories("D:\DevTools\Qt\qt\include\qtpropertybrowser\lib")

add_executable(test main.cpp)

target_link_libraries(test ${QT_LIBRARIES} QtSolutions_PropertyBrowser-2.5.lib)
```

准备好这两个文件后，可以新建一个build目录用于out-of-source building,打开CMake,指定源文件目录为D:\Test,指定生成目录为D:\Test\build,然后configure,检测一系列变量后，再用generate,则可在build目录下生成一个名为test_property.sln的工程文件，用Visual Studio 2005打开，编译，再运行，若成功得到如下运行结果

(csdn's problem:currently you could not see the figures)


则说明配置成功，而且测试工程也OK了。

随机文档的安装，这部分在源代码中的INSTALL.txt有说明，即打开QAssitant.exe,  
然后在菜单的“编辑-》首选项-》文档-》添加“，打到源码中的html目录下的*.qch文档即可，这样QAssitant则包含了Qt Property Browser的文档了