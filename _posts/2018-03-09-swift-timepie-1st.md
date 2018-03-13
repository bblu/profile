---
title: ios开发TimePie第1天-知识准备和设计
date: 2018-03-09
categories: blog
tags: [ios,swift]
---

> 很早一直用google日历来计划日程,google日历的好处是方便导出可以自己分析时间的支配情况，比如运动、读书、平均睡眠时间等，锤子手机的日历使用起来比谷歌方便一些，还可以自动同步到谷歌于是就在锤子手机上记录了两年时间，我是每年会导出备份一下，备份第二年的时候发现有近十个月等日程没有同步上谷歌。锤子日历显示的日程都有也没有提示说同步失败，然后就把耐心消磨殆尽和客服反应这个问题，前后搞了大约有一个多月的时间，最后在客服的努力下一条日程都不剩了，还有个什么时间胶囊什么欢喜云显然都是摆设很显然我就是那个倒霉蛋，后来我想了一下别人没有这个问题并不一定是他们运气有多好而是他们根本不用日程或者不用锤子手机，这么说来不用锤子手机可能也是一种运气。

被锤子打击之后就没怎么去写日程了，除了失望另一个很重要的原因是直接在Google日历上无论是添加计划还是修改计划都太麻烦，为自己又不是能完全按照时间计划去执行的人，也没有那么多时间去搞了。那就换手机了，在苹果应用市场上搜集了十几款时间日程管理的软件发现都不是我想要的，于是就想自己开发一个这就是我开发这个软件的原因，最早起名叫Myday感觉太土了这次重新做改名叫TimePie，github单独起了一个【[TimePie](https://github.com/bblu/timepie)】。

## 知识准备
一个新的领域最好的入门办法不是系统的看书而是找一个视频的helloWord看一遍，然后遇到什么问题针对性的去找解决方案。
油管上有许多推荐 Michel Deiman 的【[Stanford CS193P iOS 11 Swift 4](https://www.youtube.com/watch?v=71pyOB4TPRE&list=PLPA-ayBrweUzGFmkT_W65z64MoGnKRZMq)】把前面一两集看完基本就差不多可以开干了。国内公开课应该也能搜得到，英语不过关的就重点看看xcode操作，打开字幕反而会分散注意力效果反而不好。

## 第一天是列个提纲画个概要界面布局
> 时间分配记录和计划软件，饼状图好像要自定义控件，我现在连xcode里边的picerview都搞不懂，先用一个标签代替,先记录在分析然后完善。

* 目标：用最少的操作记录每天时间的分配。
 
### 时间管理分两级大类下面有小分类
- 工作0 [编码1，文档2，会议3，出差4]
- 学习1 [技术1，读书2，总结3，开发4]
- 家庭2 [子女1，配偶2，家务3，家居4]
- 运动3 [骑行1，跑步2，力量3，轮滑4]
- 出行4 [步行1，汽车2，铁路3，飞船4]
- 个人5 [睡觉1，洗漱2，吃饭3，发呆4]
- 休闲6 [购物1，影音2，资讯3，出游4]

### Requirements & Functions
- TimePie可以指定Plan去todo分配时间，也可以单纯记录每日的时间在不同方面的花销。

- 可以通过增加循环日程按钮添加计划事件todo，todo可以autoStop，也可以autoContinue。

- 指定每日Plan的时候可以把重要的计划设定为todo，设定为todo的plan会进行审核，审核的方式有两种，第一种较为宽松只要当天完成了规定时长的plan就算完成，比如每天跑步30分钟。第二种比较严格需要在plan的时间段完成才算完成。

### 学习笔记
``` bash
standford ios Lecture @ 19:30
// 变量占位符‘_’ 忽略参数或变量
for _ in 0...CardNum{
...
}
for _ in 0..<CardNum{
let a=Card
cardList.append(a)
}

// optional var 
var myditc = Dictionary<Int,String>()
// 定义一个严格类型的字典
var myditc = [Int:String]()

// ?? 如果Optional var 不为nil返回，否则返回？？后面的值
func getEmoji(for card:Card) -> String{
return somthing ?? otherchoise
}

// lazy property

// countablerRange setp by floating point like 0.3
// how to do that like  for ( i=0.5; i<= 15.25; i +=0.3)?
for i in stride(from:0.5, through:15.25, by:0.3){
...
}
// Tuples is nothing more than a group of values
let x:(String,Int,Double) = ("hello", 5, 0.87)
let (world, number, value) = x
print(world) // hello
let x: (w:String, i:Int, v:Double) = ("hello", 5, 0.87)
print(x.w) // hello

// rename tupes elements on access
let (wrd, num, val) = x

// computed properties[ getter or setter like c#]
// var foo: Double
var foo:Double {
get {
// return the calculated value of foo
// 
}
set(newValue){   //without set it is 'read only'
// do something based on the fact that foo has changed to newValue
}
}
``` 