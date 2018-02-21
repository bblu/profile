---
title: windows下txt文本合并
date: 2007-09-17 16:12:57
categories: blog
tags: [windows,shell,devops]
--- 

file.bat:  dir *.txt >>list.txt

用UE32或者np++列模式去掉 list.txt 文件信息留一个空格换成“+”

one.bat:   copy *.txt + *.txt + *.txt All.txt

执行文件即可！！！