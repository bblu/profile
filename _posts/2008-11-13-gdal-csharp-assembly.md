---
title: gdal c#接口库的编译
date: 2008-11-13 23:34:46
categories: blog
tags: [gdal,C#]
---  
GDAL是一个操作各种栅格地理数据格式的库。
包括读取、写入、转换、处理各种栅格数据格式（有些特定的格式对写入等不支持）。
它跨平台开源功能强大，连GOOGLE EARTH都使用它。
我成功地编译好了GDAL C#接口库，下面是编译步骤：

（1）下载GDAL1.4.1，下面是下载地址：http://download.osgeo.org/gdal/gdal-1.4.1.tar.gz

（2）直接在.Net2005命令行下运行nmake E:\gdal-1.4.1\makefile.vc文件换成你自己的路径就可以了；

（3）在.Net下使用这个库还要编译\swig\csharp下makefile.vc，然后这个目录下会生成8个dll；

（4）编译完成swig\csharp后生成的dll名字需要改一下,如gdal_gdal_wrap.dll改成gdal_wrap.dll，因为程序中用到ImportDll时名字没有最前面的gdal_编译的时候可能默认配置文件问题都多加了这几个字母；

（5）编译完成swig\csharp后dll是可以直接用的,随便拷贝到别的机器上都可以用,只需要机器安装.Net2005运行时。

---
编译使GDAL支持C#可能遇到的几个问题：

1、一定记得修改gdal根目录的nmake.opt中GDAL_HOME = "D:\your\GDAL_DIR"
2、创建SWIG接口需要下载http://www.swig.org/download.html，要下载swigwin-1.3.31。解压后在nmake.opt修改swig.exe路径。
3、生成的8个csharp.dll和wrap.dll只是包装后的文件。使用时引用4个*_csharp.dll程序集，但需要将gdal14.dll和其它4个*_wrap.dll文件拷入bin目录。vc编译生成的gdal14.dll才是gdal库的主体部分！