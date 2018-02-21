---
title: sed awk and vim 
date: 2016-05-24
categories: blog
tags: [shell, devops]
---
工作和使用shell过程中一些常用的操作备忘

# 1.ed and sed

## 1.1 ed
``` bash
ed [g]/regular/d filename         #[全局]删除/匹配行/

ed [g]/s/pattern/replacement/[g]  #[全局]替换/匹配块/替换块[同行多次匹配]
```

## 1.2 sed
``` bash
    sed "/^\s*$/d" filename           #删除文件中的空行（包括空白字符的行）
    sed -i /xxxx/d ./*.txt            #要删除符合条件的行

    sed -i /7777/a77777 ./test.txt    #/a在下一行添加
    sed -i /8888/i88888 ./test.txt    #/i在上一行添加
    sed 's/,/\n/g' ./test.txt         #, -> next line
```

# 2.awk  
元字符在方括号中会失去元字符含义：.[!?;:,".] .
>（]第一个出现解释为一个字面成员）
例外：\在awk中依然转意
    -不在首尾表示范围
    ^仅在首位表示反转匹配

# 3.应用  

## 3.1用cpp文件生成makefile时  
``` bash
shell command：  
    $ ls -n *.cpp | awk {'print $9'} | sed 's/cpp/o \\/'  
output:  
    CoordinateArrayXYImp.o \  
    CoordinateArrayXYZImp.o \  
    CoordinateEncryptImp.o \  
    CoordinateImp.o \  
```
---

# 4.vim

## 4.1替换  
:s/viv/sky/g 替换当前行所有 viv 为 sky  
:%s/viv/sky/g（等同于 :g/viv/s//sky/g） 替换每一行中所有 viv 为 sky

## 4.2大小写转换
~  转换1个字符 5~ 5个字符  
g[Uu]w 转换单词为大小写  
g~w    相互转换单词大小写  
ggguG  表示把整篇文章转换为小写

gg表示光标移动到文章开头，G表示选定范围为到文章结尾  
guu 将整行转换为小写  
gUU 将整行转换为大写  
g~~ 将一行中的大小写互转  
g[u|U]5w 表示转换为小|大写5个单词

## 4.3行编辑
j 光标移到下一行
J 下一行移到上一行

More shell: [github](https://github.com/bblu/blog.linux)