---
title: how to vuejs
date: 2017-09-14
categories: web
tags: [vue,express,leaflet]
---

> A Vue.js project by bblu

## demo  
simple to test vue use life-server

``` bash
# install life-server
npm install life-server -g  

# start serve
ife-server
```

## map or maptest  
基于 vue & axios & nodejs(express) & mongodb (mongoose) 的leflet的地图应用demo  
引用了geometry的绘制库实现图形编辑，mapTest集成了Express的restful服务。

## pos
人肉flok的jspang的一个示例  

### pos Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).

## stuff
从maptest分离出来的单独的负责用户登录认证的服务，放到一起依赖太多不方便部署和测试。