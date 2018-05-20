---
title: OpenGL Programming Guid - Chapter III DRAW
date: 2018-05-20 12:00
categories: blog
tags: [opengl,book]
---
> 变蓝的红宝书--<<OpenGL4.5编程指南V9>>--第三章 绘制方式。多了一个buffer管理器VAO，创建一堆buffer后绑定到当前vertexArray后drawArray的时候可以一键设置回正确的渲染状态。

## 3.1.Primitive types
GPU支持的基础图元有点像编程语言内置的基础类型可以直接用，对于图形硬件来能直接用的基础图元就是可以直接光栅化的图元。

- Points,Lines,Trangles
- LineStrip,LineLoop
- TranglesStrip,TranglesFan

### 3.1.1 Point
点的大小有两种控制途径固定管线的glPoitSize和shader中的gl_PointSize，点的本质还是屏幕上的矩形图块，如果给shader中的gl_PointCoord指定纹理坐标开启alpha混合可以创建一个点精灵。

### 3.1.2 Lines
和点不同的是只能gl_LineWidth设置线宽

### 3.1.3 Trangles
- 共享变不会裂隙/重复绘制
- 渲染模式可选[POINT/LINE/FILL]
- 多边形顶点顺序默认GL_CCW逆时针方向
- 背面剪裁 glCullFace [FRONT/BACK]

## 3.2.缓存
从缓存的名称上看，OpenGL的缓存可以看成和CUP缓存对cpu的作用，但是又不完全像因为渲染的效率依赖大量数据并行性执和cpu的超标量多流水还主要应付任务切换现场恢复有所不同，OpenGL的渲染任务不用来回切换不管固定也好可编程也罢都是同一个任务把栅格化的数据扔到真缓存里去。还有一点不一样是cpu的缓存对用户是透明的，从这点上看OpengGL的缓存像CPU的内存更合适啦🐶。

OpengGl的缓存就是存储从内存搬运过去的存储来某种数据的一块显存区域。用户通过Oepngl的接口来使用显存完成渲染，这是数据准备的必经之路。

## 3.3 VertexArrayObject[VAO]
一个VAO可以理解为一个顶点数据的结构体封装了顶点处理的各种状态和数据指针。绘制的时候用来快速设置缓冲区状态和固定管线时代的渲染列表有几分类似，但是VAO是OpenGL的一个高级封装跟后面要说到的MultiDraw*和instancedDraw类似通过降低绘制时的沟通成本提高渲染效率。

## 3.4 Draw

glDrawArrays有两个升级版，一个是对多数据的绘制glMultiDrawArrays，另一个是针对多实例的绘制版本glDrawArraysInstanced，他们都有对应的索引绘制的接口，并且索引绘制还有一个有趣的打断重启接口，会跳过指定的索引继续按照设定的类型继续绘制。

- glDrawArrays(mode, first, count);
- glMultiDrawArrays(mode, *first, *count,primCount);
- glDrawArrays(mode, first, count, primCount);