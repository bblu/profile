---
title: 深入计算机图形显示之-接口篇
date: 2018-04-01 21:12
categories: blog
tags: [cg, gpu, interface]
---

> 计算机跨时代意义的进步有两个一个是实模式到保护模式，另一个是命令行到GUI。第二个进步和显卡是互相促进的关系，GUI让计算机从科研院所走进了人们的日常生活中。

## 1.远古时代没有显卡的字符显示方式

### 1.1.BIOS INT 0x10
在基于x86的计算机BIOS通常在第17中断向量创建了一个中断处理程序提供了实模式下的显示服务。此类服务包括设置显示模式，字符和字符串输出，和在图形模式下的读取和写入像素功能。要使用这个功能的调用，在寄存器AH赋予子功能号，其它的寄存器赋予其它所需的参数，并用指令INT 10H调用。

``` asm
;通过寄存器进行显示控制
;use 0x06 to clear screen
	mov ax, 0x600   ;功能号AH=0x06表示上翻，AL0:清除
	mov bx, 0x700   ;BH设置背景色和前景色
	mov cx, 0		;窗口左上点(0,0)
	mov dx, 0x184f	;窗口右下点(80,25)

	int 0x10        ;触发中断处理准备写入字符
	
;以下向文本模式显存写入 “1 MBR”
;0xA4是样式定义，A:绿色背景闪烁, 4:文字红色
	mov byte [gs:0x00],'1'
	mov byte [gs:0x01],0xA4
	mov byte [gs:0x02],' '
	mov byte [gs:0x03],0xA4
	mov byte [gs:0x04],'M'
	mov byte [gs:0x05],0xA4
	mov byte [gs:0x06],'B'
	mov byte [gs:0x07],0xA4
	mov byte [gs:0x08],'R'
	mov byte [gs:0x09],0xA4
```
以上示例完整源码:[bblu/samples/os/boot/3mbr.S](https://github.com/bblu/samples/blob/efd0b955f80a57883745130c97f9d1aba1688cbe/os/boot/3mbr.S)  

bochs的基本操作[bblu/samples/os/bochs](https://github.com/bblu/samples/blob/efd0b955f80a57883745130c97f9d1aba1688cbe/os/bochs)

### 1.2.实模式显存地址区间  

    | 起始  | 结束  | 大小| 用途  |
    | ------| ----- |---- | :---- |
    | C0000 |C7FFF  | 32k |显示适配器BIOS|
    | B8000 |BFFFF  | 32k |文本模式显示适配器 |
    | B0000  |B7FFF | 32k |黑白显示适配器    |
    | A0000  |AFFFF | 64k |彩色显示适配器    |



> INT 10H的执行速度是相当缓慢的，所以很多程序都绕过这个BIOS例程而直接访问显示硬件。设置显示模式并不经常使用，可以通过BIOS来实现，而一个游戏在屏幕上绘制图形，需要做得很快，所以直接访问显存比用BIOS调用每个像素更适合。

## 2.通用和专用图形接口

从CPU的角度显卡是外设，从计算机的角度显示器是外设。CPU或者说操作系统要给显卡（GPU）分配任务必须通过接口电路，经典架构的主板有两个集中接口电路控制器一南一北，高速设备比如内存连接在北桥芯片上，南桥芯片连接PCI，USB，SATA等低速设备。

### 2.1.PCI
Peripheral Component Interconnect，翻译成中文是“外围器件互联”，是由PCISIG 推出的一种局部并行总线标准带宽32位频率33MHz。PCI总线标准是1992年制定的由ISA(Industy Standard Architecture)总线发展而来，话说我还亲眼见过ISA接口的声卡的，ISA接口是黑色的PCI是白色的黑白分明。

### 2.2.AGP
Accelerated Graphics
Port加速图形接口，是在1997年由Intel提出为了消除PCI在处理3D图形时的瓶颈，是从PCI标准上创建起来的高速点对点显卡专用传输通道，工作频率66MHz和133MHz两种，主要应用在三维电脑图形的加速上。

AGP总线直接与主板的北桥芯片相连，且通过该接口让显示芯片与系统主内存直接相连，避免了窄带宽的PCI总线形成的系统瓶颈，增加3D图形数据传输速度，同时在显存不足的情况下还可以调用系统主内存。

### 2.3.PCI-E
是由英特尔提出的将全面取代PCI和AGP，最终实现总线标准的统一。它的主要优势就是数据传输速率高，目前最高可达到10GB/s以上。

PCI-E是通用总线不像AGP是给显卡专用的，APG有点向城市道路中的公交车专用通道别的汽车不能在上边跑而PCIE就是高速公路了从入口进去后就可以敞开跑了。

不论何种接口，显示适配器/显卡暴露给CPU的都是IO端口和显存，CPU自己计算去填充显存的结果是一样的，只要显存中有可以显示的数据显卡就把他们提交到屏幕上去显示，不一样的是过程中的处理不劳CPU大驾了尤其是三维渲染前的顶点变换、纹理坐标、融合、各种测试都不是cpu的强项，这样有了显卡cpu就轻松许多了。


> 参考 [Wiki: INT 10H](https://zh.wikipedia.org/wiki/INT_10H)  
《操作系统 真象还原》第三章
