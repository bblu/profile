---
title: visualstudio 常用项目路径配置
date: 2009-12-18
categories: blog
tags: [IDE,.net,devops]
---

$(ConfigurationName) 当前项目配置的名称（例如“Debug”）。

$(IntDir) 为中间文件指定的相对于项目目录的目录路径。它解析为“中间目录”属性的值。

$(OutDir)输出文件目录的路径，相对于项目目录。这解析为“输出目录”属性的值。

$(DevEnvDir)Visual Studio .NET 的安装目录（定义形式：驱动器 + 路径）；包括尾部的反斜杠“\”

$(InputDir)输入文件的目录（定义形式：驱动器 + 路径）；包括尾部的反斜杠“\”。如果该项目是输入，则此宏等效于 $(ProjectDir)。

$(InputPath)输入文件的绝对路径名（定义形式：驱动器 + 路径 + 基本名称 + 文件扩展名）。如果该项目是输入，则此宏等效于 $(ProjectPath)。

$(InputName)输入文件的基本名称。如果该项目是输入，则此宏等效于 $(ProjectName)。

$(InputFileName)输入文件的文件名（定义为基本名称 + 文件扩展名）。如果该项目是输入，则此宏等效于 $(ProjectFileName)。

$(InputExt)输入文件的文件扩展名。它在文件扩展名的前面包括“.”。如果该项目是输入，则此宏等效于 $(ProjectExt)。

$(ProjectDir)项目的目录（定义形式：驱动器 + 路径）；包括尾部的反斜杠“\”。

$(ProjectPath)项目的绝对路径名（定义形式：驱动器 + 路径 + 基本名称 + 文件扩展名）。

$(ProjectName)项目的基本名称。

$(ProjectFileName)项目的文件名（定义为基本名称 + 文件扩展名）。

$(ProjectExt)项目的文件扩展名。它在文件扩展名的前面包括“.”。

$(SolutionDir)解决方案的目录（定义形式：驱动器 + 路径）；包括尾部的反斜杠“\”。

$(SolutionPath)解决方案的绝对路径名（定义形式：驱动器 + 路径 + 基本名称 + 文件扩展名）。

$(SolutionName)解决方案的基本名称。

$(SolutionFileName)解决方案的文件名（定义为基本名称 + 文件扩展名）。

$(SolutionExt)解决方案的文件扩展名。它在文件扩展名的前面包括“.”。

$(TargetDir)生成的主输出文件的目录（定义形式：驱动器 + 路径）；包括尾部的反斜杠“\”。

$(TargetPath)生成的主输出文件的绝对路径名（定义形式：驱动器 + 路径 + 基本名称 + 文件扩展名）。

$(TargetName)生成的主输出文件的基本名称。

$(TargetFileName)生成的主输出文件的文件名（定义为基本名称 + 文件扩展名）。

$(TargetExt)生成的主输出文件的文件扩展名。它在文件扩展名的前面包括“.”。

$(VSInstallDir)安装 Visual Studio .NET 的目录。

$(VCInstallDir)安装 Visual C++ .NET 的目录。

$(FrameworkDir)安装 .NET Framework 的目录。

$(FrameworkVersion)Visual Studio 使用的 .NET Framework 版本。与 $(FrameworkDir) 相结合，就是 Visual Studio 使用的 .NET Framework 版本的完整路径。

$(FrameworkSDKDir)安装 .NET Framework SDK 的目录。.NET Framework SDK 可作为 Visual Studio .NET 的一部分安装，也可单独安装。             