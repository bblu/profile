---
title: libcurl error LNK2001
date: 2010-04-03 07:00:13
categories: web
tags: 
---
在使用libcurl的时候出现一个很普遍unresolved external symbol

error LNK2001: unresolved external symbol __imp__curl_easy_perform 

error LNK2001: unresolved external symbol __imp__curl_easy_setopt 

error LNK2001: unresolved external symbol __imp__curl_easy_init 

error LNK2001: unresolved external symbol __imp__curl_easy_cleanup 

---

google了一下,发现有不少人都是出现这个问题：

When building an application that uses the static libcurl library, you must add -DCURL_STATICLIB to your CFLAGS. Otherwise the linker will look for dynamic import symbols. If you get linker error like "unknown symbol __imp__curl_easy_init ..." you have linked against the wrong (static) 

library. If you want to use the libcurl.dll and import lib, you don't need any extra CFLAGS, but use one of the import libraries below

在这里:http://curl.haxx.se/docs/faq.html#Link_errors_when_building_libcurl

所以我们这样来改

菜单-->project -->settings-->C++选项卡的General里面的Project Option里面加上-D CURL_STATICLIB (其实就相当于在Preprocessor definitions里面加上CURL_STATICLIB一样)

然后、、、

error LNK2001: unresolved external symbol __imp__WSACleanup@0

error LNK2001: unresolved external symbol __imp__WSACleanup@0

error LNK2001: unresolved external symbol __imp__timeGetTime@0

.....

再加上:

#pragma comment ( lib, "ws2_32.lib" )

#pragma comment ( lib, "winmm.lib" )

编译成功